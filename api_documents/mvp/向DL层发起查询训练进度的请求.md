# 向DL层发起查询训练进度的请求



[URL]: 192.168.186.128:8000/training/trains/traintask/2018072067241762338	"最后的数字是task_id"



## 一、前端向后端发起查询训练进度的请求，GET 请求

### 1、前端上传的数据

```json
无
```


### 2、返回给前端的数据

```json
# 成功返回的数据
{
    "code": 0,
    "msg": "任务请求成功",
    "percent": 54,  # 进度的 百分数  54% （percent = 100时说明训练结束）
    "model_loss":[
                    {
                        "dl_model_id":1,
                        "loss_list": [    # loss 值得列表
                                        {
                                            "cur_iter_num": 100,
                                            "loss": 0.3
                                        },
                                        {
                                            "cur_iter_num": 200,
                                            "loss": 0.4
                                        },
                                        {
                                            "cur_iter_num": 300,
                                            "loss": 0.5
                                        }
                         			 ]
                    },
                    {
                        "dl_model_id":2,
                        "loss_list": [    # loss 值得列表
                                        {
                                            "cur_iter_num": 100,
                                            "loss": 0.3
                                        },
                                        {
                                            "cur_iter_num": 200,
                                            "loss": 0.4
                                        },
                                        {
                                            "cur_iter_num": 300,
                                            "loss": 0.5
                                        }
                         ]
                    }
   				 ]
   
}


# 失败返回
{
    "code": 错误码,
    "msg": "错误信息",
}
```

