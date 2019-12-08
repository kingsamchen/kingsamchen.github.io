---
title: Toml to Golang Struct
categories: PROGRAMMING
date: 2019-12-08 21:47:16
tags: [golang, toml]
---
之前写某个东西的副产品，本质上和 https://xuri.me/toml-to-go/ 一样。

解析 Toml 仍然依赖 github.com/BurntSushi/toml 这个库。

```golang
// Eliminates _ and capitalizes the key. The key needs to be exported.
// e.g hello_world -> HelloWorld
func normalizeStructKey(key string) string {
	str := strings.ReplaceAll(key, "_", " ")
	str = strings.Title(str)
	str = strings.ReplaceAll(str, " ", "")
	return str
}

// Generates golang strcut from a given toml file.
// Result needs re-format.
func translate(data map[string]interface{}) string {
	def := ""
	for key, value := range data {
		key = normalizeStructKey(key)
		vt := reflect.ValueOf(value)
		if vt.Kind() == reflect.Map {
			def += key + " struct {\n" + translate(value.(map[string]interface{})) + "\n}\n"
		} else if vt.Kind() == reflect.Slice {
			s := "[]interface{}"
			if vt.Len() > 0 {
				s = reflect.ValueOf(vt.Index(0).Interface()).Type().String()
			}
			def += key + " []" + s + "\n"
		} else {
			def += key + " " + vt.Type().String() + "\n"
		}
	}

	return def
}

if _, err := toml.DecodeFile(tomlPath, &data); err != nil {
    return err
}
inner := translate(data)
```

支持结构嵌套。

不过生成的文本大概需要 gofmt 一下才符合格式规范。