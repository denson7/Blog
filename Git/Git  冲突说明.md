[TOC]
## 冲突文件示例
```
<<<<<<< HEAD
"Drivo_Server": {
  "url": "https://sqs.us-west-2.amazonaws.com/469124522226/tripo_drivo.fifo"
},
"Sensol_Server": {
  "url": "https://sqs.us-west-2.amazonaws.com/469124522226/sensol_vehicol.fifo"
=======,
"Sensol_Server": {
  "url": "https://sqs.us-west-2.amazonaws.com/469124522226/sensol_vehicol.fifo"
}
>>>>>>> 48babd46083a55f1612b6c3f75e93be9e45d8a33
```
说明：
```
<<<<<<< HEAD
# 这部分内容为本地修改的内容
=======
# 这部分内容为pull下来的仓库内容
>>>>>>> 48babd46083a55f1612b6c3f75e93be9e45d8a33
```

