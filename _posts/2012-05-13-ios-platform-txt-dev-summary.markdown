---
author: admin
comments: true
date: 2012-05-13 15:38:42+00:00
layout: post
slug: ios%e5%b9%b3%e5%8f%b0txt%e9%98%85%e8%af%bb%e5%99%a8%e5%bc%80%e5%8f%91%e6%91%98%e8%a6%81
title: ios平台txt阅读器开发摘要
wordpress_id: 7
categories:
- ios
---

最近做了一个IOS上的Txt阅读模块，需求主要有：



	
  1. 支持常见的文件编码：GB2312、Utf-8、Utf-16。

	
  2. 支持大txt文件阅读（意味着需要分次读取数据）。

	
  3. 支持跳转。

	
  4. 分页显示。


记录下技术要点以备日后使用。


# 一．字符编码问题





	
  1. 


## 字符集与编码方式







字符集与字符编码是两个不同概念。我们常见的GB2312，既是一种字符集，也是一种编码方式，因此我们用“GB2312”即可以表示字符集也可以指该字符集的编码方式；但对于Unicode字符集，有几种不同编码，因此我们不能说“Unicode编码”。Unicode字符集的编码方式有Utf-8、Utf-16、Utf-32等几种。另外，在windows记事本中，选择编码方式时有一项“Unicode”，还有一项“Utf-8”，我们很容易认为他们是两种平级的编码方式。事实上，这里的Unicode指的是Utf-16编码。




[![](http://liu-nan.com/wp-content/uploads/2012/05/1-300x97.png)](http://liu-nan.com/wp-content/uploads/2012/05/1.png)




我们的程序支持GB2312、Utf-8和Utf-16编码。







	
    * GB2312：在简体中文windows系统中，记事本中的ANSI实际就是GB2312编码。此编码方式下，每个英文字符占一个字节，每个中文字符占两个字节。

	
    * Utf-8：每个字符用1-4个字节表示。是目前比较流行的编码方式，对于纯英文文档有较好的空间利用率。特别对于从流中读取字符，可以从每个读取到的字节判断是否还有后续字节。

	
    *  Utf-16：2字节定长编码。对于纯英文文档，每个字符都用2字节实际上是浪费了一半的空间。另外，Utf-16编码有大小端的问题，可以从文件的前两个字节判断是big endian还是little endian。





## 2. 判断编码方式






	
    * Utf-8： 文件的头三个字符是0xef 0xbb 0xbf。

	
    * Utf-16：文件的头两个字符是0xff 0xfe（little endian）。若是0xfe 0xff则为big endian。

	
    * GB2312：其他情况统一当做GB2312处理。





## 3. NSString中的处理




从文件读取指定长度的字符串，并以特定编码方式进行输出步骤：







	
    * 打开一个文件，并移动文件指针至需要读取的位置：




    
    
    fileHandle = [[NSFileHandle fileHandleForReadingAtPath:filePath]；
    [fileHandle seekToFileOffset:startOffset];
    







	
    * 将指定长度的字节读入NSData：




    
    NSData *data = [fileHandle readDataOfLength:size];






	
    * 以特定编码方式生成NSString：




    
    
    NSStringEncoding encoding = NSASCIIStringEncoding;
    if (encodeName == EncodeName_UTF8) {
        encoding = NSUTF8StringEncoding;
    } else if (encodeName == EncodeName_GB2312) {
        encoding = CFStringConvertEncodingToNSStringEncoding(
                              kCFStringEncodingGB_18030_2000);
    } else if (encodeName == EncodeName_UTF16) {
        encoding = NSUTF16LittleEndianStringEncoding;
    } else if (encodeName == EncodeName_UTF16_BigEndian) {
         encoding = NSUTF16BigEndianStringEncoding;
    }
    NSString *string = [[NSString alloc] initWithData:data encoding:encoding];
    




# 二．确保字符的完整性




在移动文件指针到需要读取的位置时，若移动到一个字符的中间，恰好把这个字符串分成两段，则NSString是无法生成字符串的。因此必须确保我们一次读取的起始位置刚好是一个字符的首字节，终止位置是一个字符的尾字节。







	
    * Utf-16：由于是定长编码，只要确定读取边界刚好是2的整数倍即可。

	
    * Utf-8：规律为,读取指定位置的一个字节B：





如果B的第一位为0，则B为ASCII码，并且B独立的表示一个字符;




如果B的第一位为1，第二位为0，则B为一个非ASCII字符（该字符由多个字节表示）中的一个字节，并且不为字符的第一个字节编码;




如果B的前两位为1，第三位为0，则B为一个非ASCII字符（该字符由多个字节表示）中的第一个字节，并且该字符由两个字节表示;




如果B的前三位为1，第四位为0，则B为一个非ASCII字符（该字符由多个字节表示）中的第一个字节，并且该字符由三个字节表示;




如果B的前四位为1，第五位为0，则B为一个非ASCII字符（该字符由多个字节表示）中的第一个字节，并且该字符由四个字节表示;




如果B的前五位为1，第六位为0， 则B为一个非ASCII字符（该字符由多个字节表示）中的第一个字节，并且该字符由五个字节表示;




如果B的前六位为1，第七位为0， 则B为一个非ASCII字符（该字符由多个字节表示）中的第一个字节，并且该字符由六个字节表示;







	
    * GB2312:读取指定位置的一个字节B：





 如果B的第一位为0，则B为ASCII码，并且B独立的表示一个字符。




 否则尝试从某个位置（例如文件开头）到B生成字符串，若不能生成，则B为一个字符的首字节；若能生成，则可能为尾字节（也有可能生成乱码），可以把生成的字符串转换为Utf-8（或其他编码），若能够成功转换，则可以认为B为一个字符的尾字节。





# 三．计算每页大小




由于要分页显示，因此必须确定每次读取的字符串刚好能填满一页，即字符串长度为n的时候不超出一页，为n+1的时候便超出一页，如何找到这个n。用NSString的方法sizeWithFont:constrainedToSize:lineBreakMode:[2]配合二分法能够较快的确定所需字符数。






	
  1. 初始时，min为当前页的起始位置startOffset，max为文件的最后一个字符。mid为（max + min）/ 2;

	
  2. 判断从startOffset到mid组成的字符串是否大于一页，若大于，则max = mid，mid = （max + min）/ 2，否则min = mid，mid = （max + min）/ 2。

	
  3. 循环2，直到mid和max属于同一个字符，这时其前一个字符为所求。




这个算法的复杂度为logN，N为当前startOffset到文件结尾的长度。由于sizeWithFont:constrainedToSize: lineBreakMode:不能判断当前字符串长度是否刚好填满一页（只能判断是否能填下），因此算法的最好和最坏效率都是logN，即每次都需要执行logN次才能得到所求。




注意：这里计算出一页大小，从文件读出并加载到UITextView上时，经常会出现不准确的情况，即有时大小超出一页，出现滚动条，有时在一行未填满的情况下换页。这是因为UITextView加载文字时做了一些内部处理，使其实际大小不同于我们计算出的大小。最简单的解决方法是用UILabel代替UITextView，若一定要使用UITextView显示文字，则不能用sizeWithFont:constrainedToSize: lineBreakMode:计算size，而要每次用UITextView进行尝试。





# 四．性能优化




利用二分法确定每页所容纳的字符串长度时，我们是从当前文字起始位置到文件末尾之间进行查找。事实上，对于很多短文件，加载整个文件都不超过一页，因此这个算法的第一步可以改为mid = max，第一次就尝试加载整个文件。另外对于长文件，每页容纳长度仅占整个文件的一小部分，而每次从当前位置到文件末尾进行二分明显进行了很多次无效比较。我们可以定义一个每页容纳字符长度上限MAX_SIZE，使max = min + MAX_SIZE，通过缩短查找区间减少比较次数。




最后，通过Time Profiler工具，发现对于比较大的GB2312编码文件，大量的时间花费在确保字符完整性操作时，即尝试生成GB2312字符串过程中，当文件很大时，将生成很长的字符串，而这些字符串并不会真正被使用，仅仅为了判断是否能够生成。




[![](http://liu-nan.com/wp-content/uploads/2012/05/2-300x45.png)](http://liu-nan.com/wp-content/uploads/2012/05/2.png)




因此，可以对该步骤进行优化。具体方案是：读取指定位置的一个字节B，让B和其前、后字节组合，判断是否为合法字符，若其中一个不合法则可判定B的位置；若都合法则向前或向后继续尝试组合，知道出现不合法字符为止。
