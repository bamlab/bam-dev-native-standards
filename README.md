# Welcome on the BAM's Native Tribe Dev-Standards repo

## Website

Head over to [native.bam.tech](https://native.bam.tech).

## Preview locally (for BAM's Github organization members only)

### Requirements

You need `pip3` on your machine.

In order to preview locally:

- Go to https://github.com/settings/tokens
- Click on Generate a new token
- Enter a name and select the `repo` scope
- Generate the token and store it in a safe place

### Installation with pip3

Material for MkDocs Insiders can be installed with pip:

```sh
pip3 install git+https://${GH_TOKEN}@github.com/bamlab/mkdocs-material-insiders.git
pip3 install mkdocs-git-revision-date-localized-plugin
```

The GH_TOKEN environment variable must be set to the value of the personal access token you generated in the previous step. Note that the personal access token must be kept secret at all times, as it allows the owner to access your private repositories.

### Run

Open the folder in VS Code
Cmd + Shift + P and "Run build task"
Then open the link in the console (normally 127.0.0.1:8000)
