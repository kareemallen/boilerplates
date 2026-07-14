# Custom Libraries and Templates

This guide shows how to create your own template library for Boilerplates and how to use it with the CLI.

It focuses on the current runtime format used by this project: `template.json` manifests, renderable files under `files/`, and library management through the `repo` commands.

## What Boilerplates Expects

Boilerplates discovers templates from configured libraries. A library can be either:

- a git-backed checkout managed by `boilerplates repo update`
- a static local path that points to a directory on disk

Libraries are stored under the configured libraries directory for the active config file. If you run the CLI in a directory that contains a local `config.yaml`, that file is used first. Otherwise, Boilerplates uses the global config at `~/.config/boilerplates/config.yaml`.

## Create Your Own Library

Create a separate repository or local directory that contains one or more module folders. Each module folder contains templates for a single kind, such as `compose`, `helm`, `kubernetes`, `python`, or `terraform`.

### Recommended library layout

```text
my-templates/
  compose/
    nginx/
      template.json
      files/
        compose.yaml
        README.md
  terraform/
    vpc/
      template.json
      files/
        main.tf
        variables.tf
```

Each template lives in its own directory and must include:

- `template.json`
- a `files/` directory with the files to render

Template files are rendered recursively from `files/` and can use the custom delimiters supported by Boilerplates:

- `<< >>` for variables
- `<% %>` for blocks
- `<# #>` for comments

## Template Manifest

`template.json` must be a JSON object with a top-level `kind` field and a `metadata` object. A minimal manifest looks like this:

```json
{
  "slug": "nginx",
  "kind": "compose",
  "metadata": {
    "name": "Nginx",
    "description": "Containerized Nginx stack",
    "author": "Your Name",
    "date": "2026-07-14",
    "tags": ["web", "proxy"]
  },
  "variables": [
    {
      "name": "general",
      "title": "General",
      "items": [
        {
          "name": "service_name",
          "type": "str",
          "title": "Service name",
          "default": "nginx"
        },
        {
          "name": "container_port",
          "type": "int",
          "title": "Container port",
          "default": 80
        }
      ]
    }
  ]
}
```

Use `variables` to declare every value that appears in the files under `files/`. If a file references a variable that is not declared in `template.json`, validation fails.

`metadata.version` is optional, but when present it must be an object.

`metadata.draft: true` keeps a template out of normal discovery until you are ready to use it.

## Create the Files

Inside the template directory, place your renderable files under `files/` and use the custom delimiters in the content.

Example `files/compose.yaml`:

```yaml
services:
  app:
    image: nginx:latest
    container_name: << service_name >>
    ports:
      - "<< container_port >>:80"
```

## Add the Library to Boilerplates

Use the `repo` commands to register the library.

### Git-backed library

```bash
boilerplates repo add my-templates \
  --library-type git \
  --url https://github.com/you/my-templates.git \
  --branch main \
  --directory .
```

Then sync it locally:

```bash
boilerplates repo update
```

You can also refresh only one library:

```bash
boilerplates repo update my-templates
```

### Static library

If your templates are already on disk, point Boilerplates at the directory directly:

```bash
boilerplates repo add local-templates \
  --library-type static \
  --path ~/my-templates
```

Static libraries do not need syncing with `repo update`.

### View configured libraries

```bash
boilerplates repo list
```

### Remove a library

```bash
boilerplates repo remove my-templates
```

Use `--keep-files` if you want to remove only the config entry and keep the local checkout.

## Use Your Templates

The `kind` value in `template.json` becomes the CLI module name. If the template is `compose`, use the `compose` commands; if it is `terraform`, use the `terraform` commands.

Common workflow:

```bash
# List templates for a module
boilerplates compose list

# Inspect a template
boilerplates compose show nginx

# Generate the template
boilerplates compose generate nginx
```

If you have multiple libraries that contain the same template ID, Boilerplates may qualify the ID with the library name. In that case, use the qualified form shown by `list` or `search`.

## Practical Tips

- Keep template IDs unique within a library.
- Use `repo update` after changing a git-backed library.
- Put only renderable content in `files/`; keep metadata in `template.json`.
- Use draft templates while iterating, then clear the draft flag when the template is ready.
- Validate the template after adding or changing variables so you catch missing declarations early.

## Example Workflow

1. Create a repository or local directory for your templates.
2. Add one module folder per kind, such as `compose/` or `terraform/`.
3. Add one template directory per template, with `template.json` and `files/`.
4. Register the library with `boilerplates repo add`.
5. Run `boilerplates repo update` if the library is git-backed.
6. List, inspect, and generate the template with the matching module commands.

That is enough to publish your own reusable templates and use them with Boilerplates.