```
pre-commit run --all-files
```

Check where python is getting its certs:

python3 -c "import ssl; print(ssl.get_default_verify_paths())"