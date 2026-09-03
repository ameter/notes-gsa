to create:
sbx run --name \[your sandbox name\] opencode . -- web --hostname 0.0.0.0 --port 4096

to run:
sbx run \[your sandbox name\] -- web --hostname 0.0.0.0 --port 4096 

in another terminal:
sbx ports \[your sandbox name\] --publish 4096:4096