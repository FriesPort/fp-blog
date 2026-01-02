---
title: "计算项目的代码行数"
date: 2026-01-02T18:21:58+08:00
draft: true
description: ""
showHero: false
slug: "count-project-lines"
---

一个小脚本
https://pastebin.com/FW04B4UT

```js
@tool
extends EditorScript
var count_code := 0
var count_comments := 0
var count_total := 0


func _run():
	count_dir("res://")
	OS.alert("%s lines of code\n%s comments\n%s total lines" % [count_code, count_comments, count_total])


func count_dir(path: String):
	var directories = DirAccess.get_directories_at(path)
	for d in directories:
		if d == "addons":
			continue
		if path == "res://":
			count_dir(path + d)
		else:
			count_dir(path + "/" + d)
	
	var files = DirAccess.get_files_at(path)
	
	for f in files:
		if not f.get_extension() == "gd":
			continue
		var file := FileAccess.open(path + "/" + f, FileAccess.READ)
		var lines = file.get_as_text().split("\n")
		
		for line in lines:
			count_total += 1
			if line.strip_edges().begins_with("#"):
				count_comments += 1
				continue
			if line.strip_edges() != "":
				count_code += 1
```