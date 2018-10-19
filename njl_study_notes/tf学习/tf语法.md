# TF语法



1.使用 tensorboard 查看数据流图

```python
# 把数据流图写入文件
import tensorflow as tf
import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

if __name__ == '__main__':
    a = tf.constant(5, name="input_a")
    b = tf.constant(3, name="input_b")
    c = tf.multiply(a, b, name="multiply_c")
    d = tf.add(a, b, name="add_d")
    e = tf.add(c, d, name="add_e")

    sess = tf.Session()
    output = sess.run(e)

    writer = tf.summary.FileWriter("/home/njl/Desktop/njl_tf/tf_notebooks/graph_tf_1", sess.graph)
    writer.close()
    sess.close()


# 用 tensorboard 查看数据流图。先切换到相应目录下，在终端输入：
tensorboard --logdir="graph_tf_1"
```



2.使用Graph对象定义一个数据流图

```python
import tensorflow as tf
import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

if __name__ == '__main__':
    g1 = tf.Graph()

    # 在 with 中，定义 g1 的Op、张量等
    with g1.as_default():
        a = tf.constant(5, name="input_a")
        b = tf.constant(3, name="input_b")
        c = tf.multiply(a, b, name="multiply_c")
        d = tf.add(a, b, name="add_d")
        e = tf.add(c, d, name="add_e")
	
    # 创建数据流图g1的Session对象
    sess1 = tf.Session(graph=g1)

    res_c = sess1.run(c)
    print("res_c = ", res_c)

    res_e = sess1.run(e)
    print("res_e = ", res_e)
```

