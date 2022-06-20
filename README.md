# azure-cli-extension
How to create an Azure CLI Extension


## Contents

- [Environment Setup](#environment-setup)
- [Included Example](#included-example)

- Appendix
    - [Appendix A: azdev packages](#appendix-a-azdev-packages)
    - [Appendix B: azdev setup packages](#appendix-b-azdev-setup-packages)

- Links
    - [az extension](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest)
    - [azdev setup](https://github.com/Azure/azure-cli/blob/master/doc/extensions/authoring.md)
    - [extension example](https://microsoft.github.io/AzureTipsAndTricks/blog/tip200.html)

# Environment Setup
This section walks you through setting up an environment on your machine. 


### Stages
1. [Clone repositories](#clone-repositories)
2. [Create a Virtual Environment](#set-up-your-venv)
3. [Setup azdev](#set-up-azdev-in-your-venv)
4. [Validate setup](#validate-your-extension-is-visible-in-the-azcli)


## Clone repositories
Open a command prompt to this directory and clone the two repositories. 

1. The azure-cli repository (cloned to this directory and already masked out by .gitignore)
2. My own extensions repo (cloned to this directory and already masked out by .gitignore) if you want to just use the template I have. Otherwise:
    - Create a git repo for your extension
    - Clone it to this directory, mask it in .gitignore
    - Change the **azdev extension repo add" call to the name of your repo folder.

```bash
git clone https://github.com/Azure/azure-cli.git
git clone git clone https://github.com/grecoe/azure-cli-extensions.git
```

## Set up your venv
At least for me, I needed to upgrade pip to make sure that azdev would succesfully install in my virtual environment. 

```bash
pip install --upgrade pip
python -m venv clienv
clienv\Scripts\activate
pip install -r requirements.txt
```

## Set up azdev in your venv

This tool will do the heavy lifting as far as setting up the development environment for your extension work.

> <b>NOTE:</b> On the second and third lines you will likely have some changes. Those are:
> <br><br> Line 2: If you use your own repository, change the name from azure-cli-extensions to that directory name.
> <br><br>Line 3: If you are only developing on an existing extension, run: azdev setup -r <PATH> -e <NAME> where PATH is the path to the local git folder your extension resides in and NAME is the name of your extension. If you don't know the name of the extension, you can omit -e to complete the setup. Then you can run azdev extension list -o table to see which extensions are installable for your repo and add that extension with azdev extension add <NAME>. <br><br> Otherwise just use the line listed but put in a different name than ingestion.


```bash
azdev setup <- See azdev setup questions below>
azdev extension repo add azure-cli-extensions 
azdev extension create ingestion
```

This last step will ask for an alias but then error, or it did for me, however your extension is actually created. 

```bash
What is the Github alias of the person responsible for maintaining this package?
Alias: YOUR_ALIAS
unexpected error: unable to find CODEOWNERS file.
```

### azdev setup questions
This will walk you through a few things
-  Do you plan....: y
- Path (...): [RETURN]
- Do you plan to develop...: y
- Path : [RETURN]
- Install extensions...: n
- Proceed: y

This kicks off an install to your venv for the tools you'll need. Next....you need to create a test extension. 

## Validate your extension is visible in the azcli

Now you can verify that the extension is registered with your dev environment.
```bash
azdev extension list -o table
```

Produces:
```bash
Install    Name       Path
---------  ---------  -----------------------------------------------------------------------
Installed  ingestion  c:\your_clone_path\azure-cli-extension\azure-cli-extensions\src\ingestion
```

You can also check it's registered with the az cli:
```bash
az extension list -o table
```

If you have a problem with the cli version, find the file named azext_metadata.json and change the version number. 

Run it and get help on the commands. 
```bash
az ingestion -h
```


# Included Example

The example in the repository https://github.com/grecoe/azure-cli-extensions.git is called ingestion.

It's a simple extension that currently only acquires an access token provided information about a service principal in Azure Active Directory. 

Configuartion can be passed in through parameters or through a configuration file, with parameters superceding a configuration file if both are provided. 

Specific options utilized:
- Optional parameters. 
- Parameter validation.
- Client factory.  
- Customized help.

Specifically, this extension exposes the command `az ingestion token get ..`

- [Command help](#command-help)
- [Usage](#usage)

## Commands

### token
> az ingestion token get

> az ingestion token get -f ./templates/token.json

### platform
> az ingestion platform list

> az ingestion platform add -f ./templates/platform.json

> az ingestion platform show -p platform_name

> az ingestion platform remove -p platform_name



## Command help:
```bash
> az ingestion token get -h

This command is from the following extension: ingestion

Command
    az ingestion token get : Get authentication token.
        Command group 'ingestion token' is in preview and under development. Reference and
        support levels: https://aka.ms/CLI_refstatus
Arguments
    --configuration -f                : Configuration File.
    --service-principal -p            : Service Principal ID.
    --service-principal-credential -c : Service Principal Secret.
    --tenent -t                       : Active Directory Tenent.

Global Arguments
    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --only-show-errors                : Only show errors, suppressing warnings.
    --output -o                       : Output format.  Allowed values: json, jsonc, none, table,
                                        tsv, yaml, yamlc.  Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more
                                        information and examples.
    --subscription                    : Name or ID of subscription. You can configure the default
                                        subscription using `az account set -s NAME_OR_ID`.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.

Examples
    Acquire platform token
        Token can be acquired by providing the configuration direction or by providing a
        configuration json file with the settings contained within it.

        az ingestion token --tenent "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
                          --service-principal "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
                          --service-principal-credential "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        or

        Supply a configuration file, as a json dictionry, with the above fields.

        az ingestion token --configuration "local_json_with_settings"

To search AI knowledge base for examples, use: az find "az ingestion token get"

Please let us know how we are doing: https://aka.ms/azureclihats
```


## Usage:
```bash
az ingestion token get -p SERVICE_PRINCPAL_ID -c SERVICE_PRINCIPAL_CRED -t AZURE_TENENT

az ingestion token get -f token.json
```
