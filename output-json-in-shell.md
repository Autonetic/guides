# How to Output JSON format in linux Shell / Bash

### Dependencies:
  To output JSON formatted code from the command line in linux, you will need to install the JSON package from NPM.

  `sudo apt update && sudo apt install npm`


### Step one
  Next install NPM's JSON package, install it globally to be able to use it from the command line:

  `npm install --global json`

  You should see the package download and install relativley quickly.



### Step two

  Now you can use the `json` command, a helpful example:

  ```
  curl -X 'GET' \
    'https://api.xeggex.com/api/v2/asset/getbyticker/BTC' \
  -H 'accept: applications/json' | json > btc_price.json
  ```

Thats it! simple!! =)
