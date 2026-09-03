Install tools
pip install detect-secrets==1.5.0
pip3 install gibberish-detector
pip install pre-commit

Install pre-commit hooks for the repo
cd <your repo directory>
pre-commit install

Run manually (optional)
detect-secrets-hook --baseline .secrets.baseline $(git diff --name-only origin/main)