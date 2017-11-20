
## pipeline
实际的应用中数据挖掘项目会分成多个阶段来完成，数据采集，清洗到字段/属性/特征的抽取/转换，多种模型算法的组合，到模型的训练，效果评估，上线。这些步骤之间的本质是数据到数据之间的转换，和数据到模型之间的转换。
![](../images/sklern_pipeline.jpg)

spark ml 希望把这些流程做成一个流水线，主要分成5个角色：Parameters,Model,Estimator,Transformer,Evaluator，逐个来学习学习。
## Parameters
即机器学习的参数。为后面的Estimator，Transformer对象提供参数。设置这个角色的意义在于，做过机器学习的项目的同学都知道，模型的调参是提高模型效果必不可少的一部分。如果通过传参数，写map的方式进行参数设置，就会很不直观，可能调用者连有哪些参数可以使用都不清楚。针对这个问题，spark ml模块设置了这个角色，将模型可以调节的参数直观展现给用户。

LogisticRegressionModel在mllib和ml的实现是常规思路。

```
  private var threshold: Option[Double] = Some(0.5)
```
threshold作为一个变量，用户看代码时是无法区分这是个可调节的参数，还是一个全局变量。

```
private[classification] trait LogisticRegressionParams extends ProbabilisticClassifierParams
  with HasRegParam with HasElasticNetParam with HasMaxIter with HasFitIntercept with HasTol
  with HasStandardization with HasWeightCol with HasThreshold
  ```

从新的类签名，我们可以看到，LogisticRegressionModel有7个可控制参数。这个想法把LogisticRegressionModel本身变成了一个参数容器，但是问题来了，这些参数的值是如果设置到模型中的呢？

```
private[ml] trait HasThreshold extends Params {

  final val threshold: DoubleParam = new DoubleParam(this, "threshold", "threshold in binary classification prediction, in range [0, 1]", ParamValidators.inRange(0, 1))

  setDefault(threshold, 0.5)
  def getThreshold: Double = $(threshold)
}
```
关键是Params类，这个类有5个属性，[T]:类型，name:名称，doc:描述信息，defaultValue：默认值
每个参数特质继承了分别去定义这5个属性，那么这些常量是如何从类的定义中传到模型中的呢？
其实内部还是把这些参数和对应的值提取了出来，放入到map中，这样普通常量和调参的常量就区分开来了。

```
  lazy val params: Array[Param[_]] = {
    val methods = this.getClass.getMethods
    methods.filter { m =>
        Modifier.isPublic(m.getModifiers) &&
          classOf[Param[_]].isAssignableFrom(m.getReturnType) &&
          m.getParameterTypes.isEmpty
      }.sortBy(_.getName)
      .map(m => m.invoke(this).asInstanceOf[Param[_]])
  }
```
显示通过反射将所有的特这抽取出来，然后把名字和Value映射成对，放入map中，供模型使用。
```
private val paramMap: ParamMap = ParamMap.empty
```