```
keras_model.save(f, save_format='tf')
else:
    m = tf.function(lambda x: keras_model(x)) #full model
    spec = tf.TensorSpec(keras_model.inputs[0].shape, keras_model.inputs[0].dtype)
    m = m.get_concrete_function(spec)
    frozen_func = convert_variables_to_constants_v2(m)
    tfm = tf.Module()
    tfm.__call__ = tf.function(lambda x: frozen_func(x), [spec])
    tfm.__call__(im)
    tf.saved_model.save(
        tfm,
        f,
        options=tf.saved_model.SaveOptions(experimental_custom_gradients=False) if
        check_version(tf.__version__, '2.6') else tf.saved_model.SaveOptions())
    LOGGER.info(f'{prefix} export success, saved as {f} ({file_size(f):.1f} MB)')
    return keras_model, f
except Exception as e:
    LOGGER.info(f'\n{prefix} export failure: {e}')
    return None, None

def export_pb(keras_model, im, file, prefix=colorstr('TensorFlow GraphDef:')):
    #YOLOv5 TensorFlow GraphDef *.pb export https://github.com/leimao/Frozen_Graph_TensorFlow
    try:
        import tensorflow as tf
        from tensorflow.python.framework.convert_to_constants import convert_variables_to_constants_v2

        LOGGER.info(f"\n{prefix} starting export with tensorflow {tf.__version__}...")
        f = file.with_suffix('.pb')

        m = tf.function(lambda x: keras_model(x)) #full model
        m = m.get_concrete_function(tf.TensorSpec(keras_model.inputs[0].shape,
                                        keras_model.inputs[0].dtype))
        frozen_func = convert_variables_to_constants_v2(m)
        frozen_func.graph.as_graph_def()
        tf.io.write_graph(graph_or_graph_def=frozen_func.graph, logdir=str(f.parent),
                          name=f.name, as_text=False)

        LOGGER.info(f'{prefix} export success, saved as {f} ({file_size(f):.1f} MB)')
        return f
    except Exception as e:
        LOGGER.info(f'\n{prefix} export failure: {e}')

def export_tflite(keras_model, im, file, int8, data, ncalib, prefix=colorstr('TensorFlow Lite:')):
    #YOLOv5 TensorFlow Lite export
    try:
        import tensorflow as tf

        LOGGER.info(f"\n{prefix} starting export with tensorflow {tf.__version__}...")
```