---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
externalUrl: ""
summary: ""
showReadingTime: false
slug: "{{ .Name | urlize }}"
_build:
  render: "false"
  list: "local"
---
