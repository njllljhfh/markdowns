#MVP训练平台的RPC接口
##RPC 所使用的工具 [gRpc](http://doc.oschina.net/grpc?t=57966)
###使用方法

####生成proto文件

**proto文件包含函数接口定义和输入输出的定义的例子**

syntax = "proto3"            #协议定义

package mvp_rpc              #包名称

service FormatData{          #接口定义

	rpc task_stop_request(Data_task_id) returns (Data_res_Code) {}

}

message Data_task_id
{

	uint64  task_id = 1;

	uint64  interface_id = 2;

	uint64  model_id     = 3;

}

message Data_res_Code{

	uint64 resCode = 1;

}

####编译proto文件
python -m grpc_tools.protoc -I proto文件目录 --python_out=编译生成的文件目录 --grpc_python_out=编译生成的文件目录 mvp.proto

会生成两个文件mvp_pb2.py和mvp_pb2_grpc.py文件，其中mvp_pb2.py是输入输出数据的定义， mvp_pb2_grpc.py是函数的定义。

####rpc客户端的使用方法
conn = grpc.insecure_channel(_HOST + ':' + _PORT)     #_HOST 表示IP地址， _PORT 表示端口号

client = mvp_pb2_grpc.FormatDataStub(channel=conn)   #获取client后就可以调用对应的自定义函数接口和自定义的数据结构

##任务启动
**函数名称：** Data_task_request

**函数功能：** 用于启动训练或者测试任务的功能

**函数的输入：** data_task_request     #自定义的输入参数类Data_task_request


**函数的输出：** data_res_code         #返回值包含错误码的Data_res_Code

**输入输出参数中的自定义类型**

		class Data_task_request
		{

				param_request         #自定义的ParamClass类

				task_id                 #uint64

				interface_id						#uint64

				task_type								#自定义的TaskTypeClass枚举类

				json_mongodb_id　　　　　#string类型训练数据集的MongoID号

				json_val_id　　　　　　　#string类型验证集数据对应的MongoID号

				trained_model_id　　　　#string类型的模型ID号
		}

		class　ParamClass
	　｛
				model_name　　　　　　　　　　　　　　　#模型名称(string类型)

				dataset_expansion_lst								　#数据集的扩充方式，这里面list的每一个元素都是自定义的ExpansionDataClass

				net_model　　　　　　　　　　　　　　　  #基础网络名称如（resnet, vgg16) (string类型)

				model_scope														#网络规模这里用ModelScopeClass枚举定义的

				device_type                           #设备类型，这里采用DeviceClass枚举类型进行定义

				inital_learning_rate                  #初始的学习率， 这里是一个double型

				device_num                            #设备的数量（int类型）

				max_num_iters                         #最大的迭代次数（int类型）

				batch_size                            #每次放入迭代的图像数目

			  snap_shot_ivterval                    #模型的保存间隙

		｝

		enum ExpansionDataClass
		{

				HOR_FLIPE = 0;                       #水平翻转

				VER_FLIPE = 1;                       #垂直翻转

				ROTATE_90 = 2; 										   #90度旋转

				VAGUE = 3;                           #模糊

		}

		enum ModelScopeClass
		{

				SMALL = 0;													#小型规模

				NORMAL = 1;													#普通规模

				BIG = 2;                            #大型规模

		}

		enum DeviceClass
		{

			CPU = 0;                            #使用CPU

			GPU = 1;													  #使用GPU

		}

		enum TaskTypeClass
		{

			TRAIN = 0;													#训练类型

			TEST = 1;                           #测试类型

		}

		class  Data_res_Code
		{

			resCode                           #错误码（int型）

		}

**调用例子**

在前面已经得到client的情况下（已经建立了连接了）
	task_type = 0 # 0 is train, 1 is test

	param = mvp_pb2.ParamClass(model_name = 'mvp_model', dataset_expansion_lst = [0, 1, 2,3], net_model = 'res101',
	                           model_scope = 0, device_type = 0,inital_learning_rate = 0.01,  device_num = 1,
	                           max_num_iters = 50000, batch_size = 10, snap_shot_ivterval = 10000)

	task_request = mvp_pb2.Data_task_request(param_request = param, task_id = 129979767823453, interface_id = 1, task_type = task_type,
	 json_mongodb_id = '5b5ad4b19dc6d67a491f0789', json_val_id = None, trained_model_id = '')


	response = client.task_start_request(task_request)

**注意:** 在客户端调用的时候枚举一律都是传递相应的枚举值。

**打印返回值， 这里按照json的格式展示**
{'resCode', 0} 表示OK，如果大于0则表示有错误

##任务停止
**函数名称：** task_stop_request

**函数功能：** 用于停止任务

**函数的输入：**  data_id              #自定义的Data_task_id


**函数的输出：** ret_state_code        #返回值包含错误码的Data_res_Code

**输入输出参数中的自定义类型**


	class Data_res_Code
	{

		resCode                      #错误码（int型）

	}

	class Data_task_id
	{

			task_id                      #uint64   任务ID

			interface_id						     #uint64

			model_id                     #模型ID （目前传None)

	}

**调用例子**
response  = client.task_stop_request(mvp_pb2.Data_task_id(task_id = 1, interface_id = 1，model_id  = None))

**打印返回值， 这里按照json的格式展示**
{'resCode', 0} 表示OK， 如果大于0则表示有错误

##训练进度查询

**函数名称：** get_train_progresses

**函数功能：** 用于查询训练的进度

**函数的输入：**  data_id                     #自定义的Data_task_id

**函数的输出：** ret_train_records            #自定义的TrainProgressRecords类型的数据

**输入输出参数中的自定义类型**

	class Data_task_id
	{

			task_id                      #uint64   任务ID

			interface_id						     #uint64

			model_id                     #模型ID （目前传None)

	}


	class TrainProgressRecords
	{

			resCode                    #错误码(int)

			ErrorMsg                   #错误信息(string)

			model_id_lst               #model的ID号列表(List)

			train_records_lst          #自定义的TrainRecord类型的List
	}


	class TrainRecord
	{

			task_id                  #任务ID（int)

			interface_id						 #子任务ID（int）

			model_id								 #模型ID（int)

			record_time              #记录时间（double浮点）

			cur_iter_num             #当前的迭代数目（int）

			loss                     #loss值（double浮点）

			mAP											 #mAP值（double浮点）

			lr                       #学习率值（double浮点）

			max_iter_num             #最大的迭代（int）

			model_name               #模型名称（string）

	}


**调用例子**
response  = client.get_train_progresses(mvp_pb2.Data_task_id(task_id = 1, interface_id = 1， model_id = None))

**注意:** 这里的response就是TrainProgressRecords类型。目前的训练的model_id设置为None

**打印返回值， 这里按照json的格式展示**
{'resCode':0, ErrorMsg = 'OK'， model_id_lst = [1, 2, 3], 'train_records_lst':[{'task_id':1, 'interface_id':1, 'model_id':1, 'record_time':0.02, 'cur_iter_num': 100, 'loss' = 0.02, 'mAP' : 0.02, 'lr':0.001, 'max_iter_num':50000, 'model_name':'resnet_1000.cpkt'}, {'task_id':1, 'interface_id':1, 'model_id':1, 'record_time':0.02, 'cur_iter_num': 200, 'loss' = 0.02, 'mAP' : 0.02, 'lr':0.001, 'max_iter_num':50000, 'model_name':'resnet_1000.cpkt'}]}


##测试进度查询
**函数名称：** test_progress_query

**函数功能：** 用于查询测试的进度

**函数的输入：**  data_id                    #自定义的Data_task_id

**函数的输出：** ret_test_records            #自定义的TestProgressParam类型的数据

**输入输出参数中的自定义类型**

	class Data_task_id
	{

			task_id                      #uint64   任务ID

			interface_id						     #uint64

			model_id                     #模型ID

	}

	class TestProgressParam
	{

			ret_state_code              错误码（int）

			ErrorMsg                    错误信息（string)

			record_time                 记录时间（int）

			cost_time                   消耗的时间（浮点）

			img_progress_num            当前的处理的图像数目（int)

			img_sum_num                 图像总数目（int)

			end_char                    结束标识符(string) **为'c'表示继续， 为’z'表示停止**

			task_id                     任务ID (int)
	}


**调用例子**
response  = client.test_progress_query(mvp_pb2.Data_task_id(task_id = 1, interface_id = 1， model_id = None))

**注意:** 这里的response就是TestProgressParam类型。目前的训练的model_id设置为None, end_char为字符串'c'表示还在继续， 否则是为'z'表示停止

**打印返回值， 这里按照json的格式展示**
{'ret_state_code':0, 'ErrorMsg':'OK', 'record_time':1000, 'cost_time':0.34, 'img_progress_num':100， 'img_sum_num':200, 'end_char':'c', 'task_id':1, 'interface_id': 1}

##测试结果查询
**函数名称：** test_result_query

**函数功能：** 用于查询测试的结果

**函数的输入：**  data_id                     #自定义的Data_task_id

**函数的输出：** ret_test_result              #自定义的TestResultParam类型的数据

**输入输出参数中的自定义类型**

	class Data_task_id
	{

			task_id                      #uint64   任务ID

			interface_id						     #uint64

			model_id                     #模型ID

	}

	class TestResultParam
	{

			ret_state_code              #错误码（int）

			ErrorMsg                    #错误信息（string)

			task_id                      #uint64   任务ID

			interface_id						     #uint64

			json_mongodb_id              #测试结果对应的MongoId号（string)

			img_mongodb_ids              #图像MongoID 的MongoId号组成的List
	}

**调用例子**
response  = client.test_result_query(mvp_pb2.Data_task_id(task_id = 1, interface_id = 1， model_id = 1))

**注意:** 这里的response就是TestResultParam类型。这里的输入中model_id对于单模型训练的时候为None，但是对于多模型的时候需要指定

**打印返回值， 这里按照json的格式展示**
	{'ret_state_code':0, 'ErrorMsg':'OK', 'task_id':1, 'interface_id': 1，'json_mongodb_id':'2343434543', 'img_mongodb_ids':['43423545', '56436543', '65464565']}


##评价结果查询
**函数名称：** evaluation_result_query

**函数功能：** 用于查询评价的结果

**函数的输入：**  data_id                     #自定义的Data_task_id

**函数的输出：** ret_evaluate_result              #自定义的EvaluateResult类型的数据

**输入输出参数中的自定义类型**

	class Data_task_id
	{

		task_id                      #uint64   任务ID

		interface_id						     #uint64

		model_id                     #模型ID

	}

	class EvaluateResult
	{

		ret_state_code              #错误码（int）

		ErrorMsg                    #错误信息（string)

		task_id                     #uint64   任务ID

		interface_id						    #uint64

		json_mongodb_id             #评价的Json对应的MongoID(string)
	}

**调用例子**
response = client.evaluation_result_query(mvp_pb2.Data_task_id(task_id = 1, interface_id = 1， model_id = 1))

**注意:** 这里的response就是EvaluateResult类型。这里的输入中model_id对于单模型训练的时候为None，但是对于多模型的时候需要指定

**打印返回值， 这里按照json的格式展示**
	{'ret_state_code':0, 'ErrorMsg':'OK', 'task_id':1, 'interface_id': 1，'json_mongodb_id':'2343434543'}


#MVI训练平台的RPC接口

##初始化检测任务

**函数名称：** initial_defect_task

**函数功能：** 初始化检测任务， 主要是导入模型数据到内存中

**函数的输入：**  init_param                   #自定义的InitialParam

**函数的输出：** ret_evaluate_result          #自定义的ResValue类型的数据

**输入输出参数中的自定义类型**

	class  InitialParam
	{

			device_num            #设备数量（int)

			model_path_lst        #模拟名称列表(string 组成的List)
	}

	class ResValue
	{

			res_code              #错误码（int）

			res_msg						    #错误信息（string)

	}

**调用例子**
	res = client.initial_defect_task(mvp_pb2.InitialParam(device_num = 1, model_path_lst = ["ocr.data", "object_detect.cpk", "defect_detect1_cpk","defect_detect2_cpk","defect_detect3_cpk","defect_detect4_cpk"]))

**打印返回值， 这里按照json的格式展示**
	{'res_code':0, 'res_msg	':'OK'}

##完成ocr任务
**函数名称：** ocr_task

**函数功能：** 字符识别任务

**函数的输入：**  ocr_request_param           #自定义的OcrRequestParam

**函数的输出：** ocr_response_res            #自定义的OcrResponseReult类型的数据

**输入输出参数中的自定义类型**

	class OcrRequestParam
	{

		img_mongo_id            #图像的ID号（string)

	}

	class OcrResponseReult
	{

		res_code              #错误码（int）

		res_msg						    #错误信息（string)

		code_str              #检测结果（string)

	}

**调用例子**
	res = client.ocr_task(mvp_pb2.OcrRequestParam(img_mongo_id = '5b87df87c55e4f0b89a6c364')

**打印返回值， 这里按照json的格式展示**
	{'res_code':0, 'res_msg	':'OK', 'code_str':'43435345445'}


##缺陷检测任务

**函数名称：** defect_detect_task

**函数功能：** 缺陷检测任务

**函数的输入：**  detect_request_param          #自定义的DetectRequestParam

**函数的输出：**  detect_response_res          #自定义的DetectResponseReult类型的数据

**输入输出参数中的自定义类型**

	class  ImgViewInfo
	{

		image_view_id                   #视角索引号(int)

		image_mongo_id                  #图像MongoID(string)

	}

	class DetectRequestParam
	{
		product_id                    #产品ID (int)

		image_view_num                #视角数量(int) 这个就是等于img_view_lst的长度

		img_view_lst                  #自定义类ImgViewInfo视角类组成的List
	}

	class DetectResponseReult
	{

		res_code              #错误码（int）

		res_msg						    #错误信息（string)

		result_json_mongo_id  #结果json对应的MongoId(string)

	}

**调用例子**

	img_view = mvp_pb2.ImgViewInfo(image_view_id = 1, image_mongo_id = '5b67e6ca9dc6d61e81e93c7a')

	res = client.defect_detect_task(mvp_pb2.DetectRequestParam(product_id = 1000, image_view_num = 1, img_view_lst = [img_view]))

**打印返回值， 这里按照json的格式展示**
	{'res_code':0, 'res_msg	':'OK', 'result_json_mongo_id':'645645775756767'}
