WITH phone_events AS (
SELECT
JSON_SERIALIZE(message.properties.event_properties.vendor.result) AS json_data,
JSON_EXTRACT_PATH_TEXT(JSON_SERIALIZE(message.properties.event_properties.vendor.result.phonerisk.signals.phone), 'lineType', true) as line_type,
message.properties.event_properties.vendor.result.phonerisk.signals.phone.carrier as carrier,
message.properties.event_properties.vendor.result.phonerisk.score::decimal(2,2) as risk_score,
message.properties.event_properties.vendor.result.name_phone_correlation.score::decimal(2,2) as correlation_score,
logs.events.success as success
FROM logs.events
WHERE <a href="http://logs.events.name/" rel="noopener" class="external-link" target="_blank"><u>logs.events.name</u></a> LIKE '%phone confirmation vendor%' 
AND message.properties.event_properties.vendor.vendor_name = 'socure_phonerisk'
AND JSON_SERIALIZE(message.properties.event_properties.vendor.result.phonerisk.reason_codes) LIKE '%R613%'
AND cloudwatch_timestamp between '2026-06-29 00:00:00' AND '2026-06-30 23:59:59'
)
SELECT
carrier,
COUNT(*) AS total,
COUNT(CASE WHEN success = 1 THEN 1 END) AS successful,
COUNT(CASE WHEN risk_score < 0.80 THEN 1 END) AS risk_passing,
COUNT(CASE WHEN 0.84 < correlation_score THEN 1 END) AS correlation_passing,
COUNT(CASE WHEN 0.84 < correlation_score and risk_score < 0.80 THEN 1 END) AS passing_result,
COUNT(CASE WHEN json_data LIKE '%R613%' THEN 1 END) AS reason_fixed_voip,
COUNT(CASE WHEN json_data LIKE '%I611%' THEN 1 END) AS major_carrier,
COUNT(CASE WHEN json_data LIKE '%R617%' THEN 1 END) AS non_major_carrier,
COUNT(CASE WHEN json_data LIKE '%I618%' THEN 1 END) AS resolved_to_individual,
COUNT(CASE WHEN json_data LIKE '%R608%' THEN 1 END) AS not_resolved_to_individual
FROM phone_events
GROUP BY carrier
ORDER BY passing_result DESC