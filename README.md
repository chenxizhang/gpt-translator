# gpt-translator for Github

## Overview

This is a Github action designed to translate the content of the changed files in the repository using OpenAI service, currently we support both OpenAI service and Azure OpenAI Service. You can use this action to translate the content of the changed files in the repository to the target language(s) you want.  

## Usage

### Basic Usage

```yml
name: translate zh to en

on:
  push:
    branches: master
    paths:
      - "docs/zh/*.md"

jobs:
  job1:
    name: job1
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: translate
        uses: chenxizhang/gpt-translator@v2
        with:
          openai_api_key: ${{secrets.OPENAI_API_KEY}}
          files: docs/zh/*.md
          to_language: en
```

### Use Azure OpenAI Service

```yml
name: translate zh to en (use Azure OpenAI Service)

on:
  push:
    branches: master
    paths:
      - "docs/zh/*.md"

jobs:
  job1:
    name: job1
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: translate
        uses: chenxizhang/gpt-translator@v2
        with:
          openai_api_key: ${{secrets.OPENAI_API_KEY_AZURE}}
          files: docs/zh/*.md
          to_language: en
          useAzure: true
          endpoint: ${{secrets.OPENAI_API_AZURE_ENDPOINT}}
```

### Use a template from prompt library


```yml
name: translate zh to en (use prompt library)

on:
  push:
    branches: master
    paths:
      - "docs/zh/*.md"

jobs:
  job1:
    name: job1
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: translate
        uses: chenxizhang/gpt-translator@v2
        with:
          openai_api_key: ${{secrets.OPENAI_API_KEY}}
          files: docs/zh/*.md
          systemPrompt: lib:gpt-translator-md
          to_language: en
```




### Translate to multiple languages

```yml
name: translate zh to en and fr

on:
  push:
    branches: master
    paths:
      - "docs/zh/*.md"

jobs:
  job1:
    name: job1
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: translate
        uses: chenxizhang/gpt-translator@v2
        with:
          openai_api_key: ${{secrets.OPENAI_API_KEY}}
          files: docs/zh/*.md
          to_language: en,fr
```

### Customize the rules and style

```yml
name: translate zh to en in casual style with rules

on:
  push:
    branches: master
    paths:
      - "docs/zh/*.md"

jobs:
  job1:
    name: job1
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: translate
        uses: chenxizhang/gpt-translator@v2
        with:
          openai_api_key: ${{secrets.OPENAI_API_KEY}}
          files: docs/zh/*.md
          style: casual
          to_language: en
          rules: |-
            - Don't change the structure
            - Don't change the meaning
```

## Inputs

You can find all the inputs below, please read the description carefully to understand how to use them.

```yml
openai_api_key:
  description: "openai api key, both OpenAI service and Azure OpenAI Service use the same parameter, and we highly recommeneded you to store this key in the secrets."
  required: true
files:
  description: "files to translate. You can use glob patterns to match multiple files. for example, docs/zh/*.md. Please note, this action will only translate those that files are changed in the pull request or push event."
  required: true
systemPrompt:
  description: "system prompt for OpenAI service. You can set it directly here, or reference to a file (like system_prompt.md) in the repository, or use a URL to get the system prompt(like https://yourwebsite.com/system.md), you can even use a template from prompt library (like lib:gpt-translator-md). This is the magic from the OpenAI PowerShell Module (https://github.com/chenxizhang/openai-powershell). For all the kinds of prompt, you can specific a placeholder of variable in the prompt, like {{to_language}}, then you can replace the value during the runtime."
  required: true
  default: |-
    You help me to translate the content from {{from_language}} to {{to_language}}, in {{style}} style. 
    You mustn't change the content structure and anything else, you just translate the content based on your language skill.
    You also follow the below user specific rules (especially for this doc format or the context) if present.
    {{rules}}
from_language:
  description: "from language, you can set one language in a time. You can use the language code like zh, en, fr, etc, or zh-cn, en-us, fr-fr, etc. Please make sure the language code is in the path of the file, for eaxmple, docs/zh/*.md, the language code is zh."
  required: true
  default: zh
to_language:
  description: "to language(s), you can set one or more languages in a time, separate by comma(,).You can use the language code like zh, en, fr, etc, or zh-cn, en-us, fr-fr, etc. The translated files will have the same file name, but placed in the target language folder. For example, if the file is docs/zh/*.md, and the to_language is en, the translated file will be docs/en/*.md."
  required: true
  default: en
modelOrdeployment:
  description: "The gpt model you want to use, like gpt-4-turbo, gpt-3.5-turbo. if you are using Azure OpenAI,set the deployment name here."
  default: gpt-4-turbo
useAzure:
  description: "If you are using Azure OpenAI Service, set this parameter to true, or 1."
  default: "false"
endpoint:
  description: "if you are using Azure OpenAI Service, set this parameter to the endpoint of your deployment."
commit:
  description: "commit the translated files to the repository"
  default: "true"
style:
  description: "The style of the translation, like formal, informal, friendly, professional, etc."
  default: professional
rules:
  description: "The rules of the translation, like don't change the structure, don't change the meaning, etc."
  default: ""
```