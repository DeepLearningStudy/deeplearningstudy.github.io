---
title: MNIST Tutorial
tags: [TensorFlow]
keywords: start, introduction, begin, install, build, hello world,
last_updated: August 12, 2015
summary: "Koosy님이 작성 하려고 준비중 입니다. 누가 먼저 뺏어가셔도 좋습니다."
---

# MNIST 데이터 다운로드 및 읽어오기


    import input_data
    mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)

    Extracting MNIST_data/train-images-idx3-ubyte.gz
    Extracting MNIST_data/train-labels-idx1-ubyte.gz
    Extracting MNIST_data/t10k-images-idx3-ubyte.gz
    Extracting MNIST_data/t10k-labels-idx1-ubyte.gz


# 텐서플로우 로딩


    import tensorflow as tf
    x = tf.placeholder("float", [None, 784])


    W = tf.Variable(tf.zeros([784,10]))
    b = tf.Variable(tf.zeros([10]))


    y = tf.nn.softmax(tf.matmul(x,W) + b)

# 트레이닝


    y_ = tf.placeholder("float", [None,10])


    cross_entropy = -tf.reduce_sum(y_*tf.log(y))


    train_step = tf.train.GradientDescentOptimizer(0.01).minimize(cross_entropy)


    init = tf.initialize_all_variables()


    sess = tf.Session()
    sess.run(init)


    for i in range(1000):
      batch_xs, batch_ys = mnist.train.next_batch(100)
      sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})

# Evaluation


    correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))


    accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))


    print sess.run(accuracy, feed_dict={x: mnist.test.images, y_: mnist.test.labels})

    0.9088

