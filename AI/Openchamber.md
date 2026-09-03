qsbx create --name idp2 opencode-openchamber --kit "git+https://github.com/protyposis/sbx-kits.git#dir=opencode-openchamber" . /Users/christopherrameter/Developer/ameter-docs


sbx secret set-custom -g --host api.gsa.usai.gov --env USAI_API_KEY --value "$USAI_API_KEY" --placeholder sbx-cs-Z86TwA8zyxWJcLfQ



```
sbx secret rm -g --host api.gsa.usai.gov  
sbx secret set-custom -g --host api.gsa.usai.gov --env USAI_API_KEY --value "$USAI_API_KEY" --placeholder USAI_API_KEY 
```



```
cd

curl -fsSL https://raw.githubusercontent.com/btriapitsyn/openchamber/main/scripts/install.sh > install.sh

chmod 700 install.sh

./install.sh

OPENCHAMBER_ALLOW_UNAUTHENTICATED_LAN=true openchamber --lan --port 3000
```