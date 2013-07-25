---
layout:     post
title:      iOS核心动画简介
category:   ios
---

核心动画（Core Animation）提供了一套API用来方便地在iOS（和Mac）上创建动画。
CALayer和CAAnimatin是核心动画中最重要的两个类。所有动画都是通过修改CALayer中的动画属性（Animatable Property）值来实现的；而CAAnimation提供了一些子类和方法用来修改这些动画属性。
![Core Animation](/images/core_animation/intro.jpeg)

##Layer（CALayer)
###Layer的几何属性
	* position
	   一个 CGPoint 的值,指定Layer相对于它父Layer的位置, 该基于父Layer的坐标系。
	* anchorPoint
		一个CGPoint值,它指定了一个基于图层bounds的符合位置坐标系的位置。anchorPoint指定了bounds相对于position的值,同时也作为变换时候的支点。改变anchorPoint的值将影响其transform(变换矩阵)的作用效果，如图所示。

anchorPoint = (0.5, 0.5):

![anchorPoint](/images/core_animation/anchor_point1.jpeg)

anchorPoint = (0, 0):

![anchorPoint](/images/core_animation/anchor_point2.jpeg)

	* frame和bounds
	  bounds属性是一个CGRect的值,指定layer的大小(bounds.size)和原点(bounds.origin)。当重写layer绘制方法时,bounds的原点可以作为图形上下文的原点。
     frame也是一个CGRect的值,它是通过position,bounds,anchorPoint和transform计算出来的。
     设置frame将会相应的改变layer的position和bounds属性,但是frame本身并没有被保存。此时，bounds的origin不受干扰,bounds的size变为frame的size；position被设置为相对于anchorPoint的适合位置。
	* 变换矩阵(transform)
	transform的值是一个4x4的矩阵。通过矩阵乘法将layer的坐标变化为一组新坐标并进行渲染。

矩阵乘法：

![矩阵乘法](/images/core_animation/matrix_multiplication.jpeg)

进行旋转、放大等操作的变换矩阵:

![矩阵操作](/images/core_animation/matrix_operation.jpeg)


有两种方法修改transform的值。

	* 将新的矩阵赋值给transform：

    	layer.transform = CATransform3DIdentity;

	* 通过键值路径进行改变：

       [myLayer setValue:[NSNumber numberWithInt:0] forKeyPath:@"transform.rotation.x"];

###CALayer的内容
 一个CALayer的内容就是一块可见的位图数据，有三种方法可以设置layer的数据。

* 将一个CGImageRef对象直接赋值给layer的contents属性。（适用于content内容很少改变的情况）

<pre>
CALayer *theLayer;
//create the layer and set the bounds and position
theLayer=[CALayer layer];
theLayer.position=CGPointMake(50.0f,50.0f);
theLayer.bounds=CGRectMake(0.0f,0.0f,100.0f,100.0f);
// set the contents property to a CGImageRef
// specified by theImage (loaded elsewhere)
theLayer.contents=theImage;
</pre>

* 实现layer的delegate方法。（适用于content内容经常改变的情况)

<pre>
- (void)displayLayer:(CALayer *)theLayer
{
    // Check the value of some state property
    if (self.displayYesImage) {
        // Display the Yes image
        theLayer.contents = [someHelperObject loadStateYesImage];
    }
    else {
        // Display the No image
        theLayer.contents = [someHelperObject loadStateNoImage];
} }


- (void)drawLayer:(CALayer *)theLayer inContext:(CGContextRef)theContext
{
CGMutablePathRef thePath = CGPathCreateMutable();
      CGPathMoveToPoint(thePath,NULL,15.0f,15.f);
      CGPathAddCurveToPoint(thePath,
                            NULL,
                            15.f,250.0f,
                            295.0f,250.0f,
                            295.0f,15.0f);
      CGContextBeginPath(theContext);
      CGContextAddPath(theContext, thePath);
      CGContextSetLineWidth(theContext, 5);
      CGContextStrokePath(theContext);
      // Release the path
      CFRelease(thePath);
  }
</pre>

* 实现CALayer的子类，并重写其绘制方法。（适用于需要改变layer基本绘制行为的情况）

<pre>
- (void)drawInContext:(CGContextRef)theContext
{
    CGMutablePathRef thePath = CGPathCreateMutable();
    CGPathMoveToPoint(thePath,NULL,15.0f,15.f);
    CGPathAddCurveToPoint(thePath,
                            NULL,
                            15.f,250.0f,
                            295.0f,250.0f,
                            295.0f,15.0f);
    CGContextBeginPath(theContext);
    CGContextAddPath(theContext, thePath );
    CGContextSetLineWidth(theContext,self.lineWidth);
    CGContextSetStrokeColorWithColor(theContext,self.lineColor);
    CGContextStrokePath(theContext);
    CFRelease(thePath);
}
</pre>

另外，通过修改CALayer的contentsGravity属性可以修改图层contents图片的位置或拉伸方式。

![contensGravity](/images/core_animation/contents_gravity.jpeg)

###CALayer的样式
* 背景和边框

<pre>
//设置背景颜色
myLayer.backgroundColor = [UIColor greenColor].CGColor;
//设置边框颜色

myLayer.borderColor = [UIColor blackColor].CGColor;
//设置边框宽度
myLayer.borderWidth = 3.0;
</pre>

* 设置圆角

<pre>
myLayer.cornerRadius = radius;
</pre>

![cornerRadius](/images/core_animation/corner_radius.jpeg)

* 阴影

<pre>
self.layer.shadowColor = [UIColor redColor].CGColor;
self.layer.shadowOffset = CGSizeMake(1, 3);
self.layer.shadowOpacity = 0.5;
self.layer.shadowRadius = 3;
</pre>

* 透明度

<pre>
self.layer.opacity = 0.5;
</pre>

* CALayer的其他子类

CALayer还提供了一些子类用来实现特定的用途

<table>
   <tr>
      <td>          类名                                  用途  </td>
   </tr>
   <tr>
      <td>CAEmmiterLayer                 实现粒子效果</td>
   </tr>
   <tr>
      <td>CAGradientLayer                实现渐变效果 </td>
   </tr>
   <tr>
      <td>CAEAGLLayer                使用OpenGL ES绘制</td>
   </tr>
   <tr>
      <td>CAReplicationLayer        批量复制并显示指定layer</td>
   </tr>
   <tr>
      <td>CAScrollLayer                   可以滚动的layer</td>
   </tr>
   <tr>
      <td>CAShapeLayer                 绘制三次贝塞尔曲线（cubic Bezier spline）</td>
   </tr>
   <tr>
      <td>CATextLayer                用于绘制文字</td>
   </tr>
   <tr>
      <td>CATiledLayer                将大图片切成小图分块展示</td>
   </tr>
   <tr>
      <td>CATransformLayer                绘制3D对象</td>
   </tr>
   <tr>
      <td>QCCompositionLayer    配合Quartz Composer绘制界面(for OS X only)</td>
   </tr>
</table>

##动画 (CAAnimation)
###隐式动画
直接对Layer动画属性进行的所有赋值操作，默认都会触发动画效果，例如：

<pre>
// assume that the layer is current positioned at (100.0,100.0)
theLayer.position=CGPointMake(500.0,500.0);
</pre>


值得注意的是，如果一个Layer对象存在一个对应的view，则该Layer没有隐式动画。

隐式动画的默认duration是0.25秒。使用CATransaction可以改变隐式动画的默认时间和时间函数，以及动画结束后的处理等属性。

###显式动画

Core Animation提供了一个显式的动画模型。该模型需要显示创建一个动画对象,并设置开始和结束的值。

显式动画不会自动执行,直到把该动画应用到某个Layer上。

![Animations](/images/core_animation/animations.jpeg)

* CAAnimation

所有layer动画的基类。

timingFunction是CAAnimation中最常用属性。它用于计算动画起点和终点之间之间各时间阶段的流逝，即动画的运行节奏：是均匀变化、先快后慢或是先慢后快。

CAMediaTimingFunction提供了两个方法用来生成timingFunction：

	* <pre>+ (id)functionWithName:(NSString *)name </pre>

可用的function name有这几种：

   > kCAMediaTimingFunctionLinear
   > kCAMediaTimingFunctionEaseIn
   > kCAMediaTimingFunctionEaseOut
   > kCAMediaTimingFunctionEaseInEaseOut
   > kCAMediaTimingFunctionDefault

效果如图所示：

![timingFunctionName](/images/core_animation/timingFunction.jpeg)

	* <pre>+ (id)functionWithControlPoints:(float)c1x :(float)c1y :(float)c2x :(float)c2y</pre>

通过四个点，生成一条三次贝瑟尔曲线（cubic Bézier curve）作为控制函数。

假如上面这些不能满足需求，则需要用到CAKeyframeAnimation自己实现timingFunction。


* CABasicAnimation

常用的动画类型，主要属性有：
	* fromValue
   
		起始值

   	* toValue

   		终止值

	* byValue

		变化值

这三个值不能多于两个为空。一旦设定了这些value，并启动动画，动画就会开始按照timingFunction设定的节奏开始执行。

<pre>
CABasicAnimation* fadeAnim = [CABasicAnimation animationWithKeyPath:@"opacity"];
fadeAnim.fromValue = [NSNumber numberWithFloat:1.0];
fadeAnim.toValue = [NSNumber numberWithFloat:0.0];
fadeAnim.duration = 1.0;

[theLayer addAnimation:fadeAnim forKey:@"opacity"];

// Change the actual data value in the layer to the final value.
theLayer.opacity = 0.0;
</pre>

* CAKeyframeAnimation
当系统提供的timingFunction不能满足需求，这时候就需要用到关键帧动画。

关键帧动画可以设定数量不定的帧，帧之间的变化由插值计算。可以通过给定values和path两种方式设定关键帧。

在使用value写关键帧动画时，value的元素类型必须和需要改变的Layer属性对应。例如要改变Layer的frame，则value必须为CGRect类型（封装成NSValue对象）；改变Layer的contents时value为CGImageRef类型。

当value中元素类型为CGPoint时，可以生成一个CGPathRef对象并赋值给path属性。此时，value属性会被忽略。

<pre>
  //create a CGPath that implements two arcs (a bounce)
  CGMutablePathRef thePath = CGPathCreateMutable();
  CGPathMoveToPoint(thePath,NULL,74.0,74.0);
  CGPathAddCurveToPoint(thePath,NULL,74.0,500.0,
                                     320.0,500.0,
                                     320.0,74.0);
  CGPathAddCurveToPoint(thePath,NULL,320.0,500.0,
                                     566.0,500.0,
                                     566.0,74.0);

  CAKeyframeAnimation * theAnimation;
  //Create the animation object, specifying the position property as the key path.
  theAnimation=[CAKeyframeAnimation animationWithKeyPath:@"position"];
  theAnimation.path=thePath;
  theAnimation.duration=5.0;
  // Add the animation to the layer.
  [theLayer addAnimation:theAnimation forKey:@"position"];
</pre>

关键帧动画中另一个重要的属性是calculationMode，它定义了给定帧之间进行差值计算的方法。

   * kCAAnimationLinear;

     两个点之间的直线上进行插值计算

   * kCAAnimationDiscrete;

     不进行插值计算，只用给定的离散点

   * kCAAnimationPaced;

     默认情况下，每两个关键帧之间的duration=总时间 /（帧数-1），具体表现就是关键帧密集的地方动画慢，关键帧稀疏的地方动画快。kCAAnimationPaced使得动画全程匀速进行。

   * kCAAnimationCubic;

     在两个点之间的圆滑曲线上进行插值计算

   * kCAAnimationCubicPaced;

     综合kCAAnimationCubic和kCAAnimationPaced

* CAAnimatinGroup

CAAnimatinGroup可以让多个动画同时执行。若group的duration大于动画时间，以动画时间为准；若小于则以group的duration为准。

<pre>
  //Animation 1
  CAKeyframeAnimation* widthAnim = [CAKeyframeAnimation animationWithKeyPath:@"borderWidth"];
  NSArray* widthValues = [NSArray arrayWithObjects:@1.0, @10.0, @5.0, @30.0, @0.5, @15.0, @2.0, @50.0, @0.0, nil];
  widthAnim.values = widthValues;
  widthAnim.calculationMode = kCAAnimationPaced;
  //Animation 2
  CAKeyframeAnimation* colorAnim = [CAKeyframeAnimation
  animationWithKeyPath:@"borderColor"];
NSArray* colorValues = [NSArray arrayWithObjects:(id)[UIColor greenColor].CGColor, (id)[UIColor redColor].CGColor, (id)[UIColor blueColor].CGColor, nil];
  colorAnim.values = colorValues;
  colorAnim.calculationMode = kCAAnimationPaced;

  //Animation group
  CAAnimationGroup* group = [CAAnimationGroup animation];
  group.animations = [NSArray arrayWithObjects:colorAnim, widthAnim, nil];
  group.duration = 5.0;
  [myLayer addAnimation:group forKey:@"BorderChanges"];
</pre>

##CAMediaTiming
CAMediaTiming是一个带有层级关系的时间系统，建立了对象和其父对象的时间映射关系。CALayer和CAAnimation都遵循的协议，主要是一些和时间相关的属性和方法。

	* beginTime
		标识动画相对于其父对象的起始时间。

        如果想让一个动画延迟两秒开始执行，可以设置一个动画的beginTime为CACurrentMediaTime()+2。

		在一个AnimationGroup中，某一个动画的beginTime为n，则意味着该动画在group开始执行的n秒后开始执行。

	* timeOffset

		标识一个时间偏移。若一个动画的duration为5秒，timeOffset为1，则该动画先执行第2-5秒的效果，再执行第一秒的效果。

	* repeatCount

		动画重复次数。

	* repeatDuration     

		动画重复执行的时间。

	* duration     

		动画耗时。

	* speed

		动画的流逝速度，和父Layer叠加。即一个speed为2的动画，加在一个speed为2的Layer上，则duration变为原时间的1/4。

	* autoreverses

		为YES时，动画执行完后会倒着再执行一次。

	* fillMode

		动画执行完后对象的行为。fillMode只有在CAAnimation的属性removedOnCompletion为NO的情况下有效。

		* kCAFillModeRemoved

          默认值，动画开始前和接收后，对Layer没有影响。即动画解释后Layer恢复到初始状态。

     	* kCAFillModeForwards

			动画结束后Layer保持最后的状态。

		* kCAFillModeBackwards

			动画被加入layer中时，layer便处于动画的初试状态。（在设置了beginTime的时候可以看出效果）

		* kCAFillModeBoth

			同时具有kCAFillModeForwards和kCAFillModeBackwards特性。

		* kCAFillModeFrozen

			同kCAFillModeForwards，已被废弃。

