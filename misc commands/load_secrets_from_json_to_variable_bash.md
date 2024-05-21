# load value from a json file using `bash`
Assumes that secrets are in a `json` file in `.secrets` folder at root in file named `secrets.json`.

___
Requires: `jq`>>> [`sudo apt install jq -y # debian`]

___

```bash
# print it
jq -r .github_access_token ~/.secrets/secrets.json 
# >> Here `github_access_token` is key from the json, of which we want to extract value & `~/.secrets/secrets.json` is the file path. 
# >> For slicing through json, read documentation.
```

```sh
# put in variable
the_GITHUB_TOKEN=$(jq -r .github_access_token ~/.secrets/secrets.json)
# >> Here `the_GITHUB_TOKEN` is the variable name
```

```sh
# print it (not recommended, beats the purpose)
echo "$the_GITHUB_TOKEN"
```

## Resource
- [how-to-parse-json-files-on-the-linux-command-line-with-jq](https://www.howtogeek.com/529219/how-to-parse-json-files-on-the-linux-command-line-with-jq/) from `howtogeek`, accessed on May 17, 24.
- [extract-a-string-from-a-json-file-and-put-into-a-variable-linux](https://askubuntu.com/questions/1357926/how-to-extract-a-string-from-a-json-file-and-put-into-a-variable-linux) from `askubuntu`, accessed on May 17, 24.