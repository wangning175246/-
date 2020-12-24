#### ES 基础命令



#### 倒排索引



#### template 

ES在创建索引的时候会进行一次模板匹配，如果匹配到了模板，则会把模板中定义的setting 和mapping 应用到该索引。

模板只会在创建索引时生效。

这样就不必为每个索引定义setting和mapping了

在创建索引的时候不能指定模板中有的mapping和属性有模板和设置生命周期，需要es7.0

```json
POST _template/fy-log
{
    "order" : 0,
    "version" : 1,
    "index_patterns" : [
      "fy-*"
    ],
    "settings" : {
      "index" : {
        "number_of_shards" : "3",
        "number_of_replicas":"0",
        "refresh_interval" : "5s"
      },
      "index.lifecycle.name": "my_policy"
    }
}
```

logstash 会向ES中创建模板，模板的匹配规则是

```json
"logstash" : {
	"order": 0,
	"version": 60001,
	"index_patterns": [
		"logstash-*"
	],
	"settings": {
		"index": {
			"refresh_interval": "5s"
		}
	},
	"mappings": {
		"_default_": {
			"dynamic_templates": [{
					"message_field": {
						"path_match": "message",
						"match_mapping_type": "string",
						"mapping": {
							"type": "text",
							"norms": false
						}
					}
				},
				{
					"string_fields": {
						"match": "*",
						"match_mapping_type": "string",
						"mapping": {
							"type": "text",
							"norms": false,
							"fields": {
								"keyword": {
									"type": "keyword",
									"ignore_above": 256
								}
							}
						}
					}
				}
			],
			"properties": {
				"@timestamp": {
					"type": "date"
				},
				"@version": {
					"type": "keyword"
				},
				"geoip": {
					"dynamic": true,
					"properties": {
						"ip": {
							"type": "ip"
						},
						"location": {
							"type": "geo_point"
						},
						"latitude": {
							"type": "half_float"
						},
						"longitude": {
							"type": "half_float"
						}
					}
				}
			}
		}
	},
	"aliases": {}
}

```

