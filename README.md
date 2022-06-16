# azure-cli-extension
How to create an Azure CLI Extension


## Contents

- [Environment Setup](#environment-setup)

- Appendix
    - [Appendix A: azdev packages](#appendix-a-azdev-packages)
    - [Appendix B: azdev setup packages](#appendix-b-azdev-setup-packages)

- Links
    - [az extension](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest)
    - [azdev setup](https://github.com/Azure/azure-cli/blob/master/doc/extensions/authoring.md)

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

