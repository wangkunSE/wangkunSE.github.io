---
title: LengthFieldBasedFrameDecoder官方文档翻译
date: 2017-09-25 15:37:41
categories: [Java]
tags:
- Java
- NIO
---

## Netty的LengthFieldBasedFrameDecoder解码器官方文档翻译



> ```html
> * A decoder that splits the received {@link ByteBuf}s dynamically by the
> * value of the length field in the message.  It is particularly useful when you
> * decode a binary message which has an integer header field that represents the
> * length of the message body or the whole message.
> * <p>
> * {@link LengthFieldBasedFrameDecoder} has many configuration parameters so
> * that it can decode any message with a length field, which is often seen in
> * proprietary client-server protocols. Here are some example that will give
> * you the basic idea on which option does what.
> *
> ```



> 这是一个通过收到的消息的长度值动态分割消息的解码器。当你解码的二进制消息拥有一个整型的头部域（其中包含了消息体或整个消息的长度）时是非常有用的。
>
> 它有非常多的配置参数，因此它可以通过长度域解码任何消息，这在平常的客户端和服务器的协议中是很常见的。这儿有一些例子将会使你对这些参数做什么有个大概的了解。

------

> ```html
> * <h3>2 bytes length field at offset 0, do not strip header</h3>
> *
> * The value of the length field in this example is <tt>12 (0x0C)</tt> which
> * represents the length of "HELLO, WORLD".  By default, the decoder assumes
> * that the length field represents the number of the bytes that follows the
> * length field.  Therefore, it can be decoded with the simplistic parameter
> * combination.
> * <pre>
> * <b>lengthFieldOffset</b>   = <b>0</b>
> * <b>lengthFieldLength</b>   = <b>2</b>
> * lengthAdjustment    = 0
> * initialBytesToStrip = 0 (= do not strip header)
> *
> * BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
> * +--------+----------------+      +--------+----------------+
> * | Length | Actual Content |----->| Length | Actual Content |
> * | 0x000C | "HELLO, WORLD" |      | 0x000C | "HELLO, WORLD" |
> * +--------+----------------+      +--------+----------------+
> * </pre>
> *
> ```



> 例子一：2字节长偏移量为0，不去头
>
> 在这个例子中值的长度为12（0x0c），就是“HELLO, WORLD ”的长度。默认情况下，该解码器会假定长度域代表了跟在长度域后面的字节数。因此，它可以用最简单的参数组合解码。
>
> ```html
> <b>lengthFieldOffset</b>   = <b>0</b>
> * <b>lengthFieldLength</b>   = <b>2</b>
> * lengthAdjustment    = 0
> * initialBytesToStrip = 0 (= 不去头)
> 
> BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
>  * +--------+----------------+      +--------+----------------+
>  * | Length | Actual Content |----->| Length | Actual Content |
>  * | 0x000C | "HELLO, WORLD" |      | 0x000C | "HELLO, WORLD" |
>  * +--------+----------------+      +--------+----------------+
>  * </pre>
> ```
>
> 



------



> ```html
> * <h3>2 bytes length field at offset 0, strip header</h3>
> *
> * Because we can get the length of the content by calling
> * {@link ByteBuf#readableBytes()}, you might want to strip the length
> * field by specifying <tt>initialBytesToStrip</tt>.  In this example, we
> * specified <tt>2</tt>, that is same with the length of the length field, to
> * strip the first two bytes.
> * <pre>
> * lengthFieldOffset   = 0
> * lengthFieldLength   = 2
> * lengthAdjustment    = 0
> * <b>initialBytesToStrip</b> = <b>2</b> (= the length of the Length field)
> *
> * BEFORE DECODE (14 bytes)         AFTER DECODE (12 bytes)
> * +--------+----------------+      +----------------+
> * | Length | Actual Content |----->| Actual Content |
> * | 0x000C | "HELLO, WORLD" |      | "HELLO, WORLD" |
> * +--------+----------------+      +----------------+
> * </pre>
> *
> ```



> 例子二: 2字节的长度域，偏移量为0，去头
>
> 因为我们通常可以调用ByteBuf的readableBytes()方法获得内容的长度，所以你可能会想且可以通过**initialBytesToStrip**去掉长度域。在这个例子中，我们指定其值为2，它和长度域的长度是相同的，去掉头两个字节。
>
> ```
> * <pre>
> * lengthFieldOffset   = 0
> * lengthFieldLength   = 2
> * lengthAdjustment    = 0
> * <b>initialBytesToStrip</b> = <b>2</b> (= 长度域的长度)
> *
> * BEFORE DECODE (14 bytes)         AFTER DECODE (12 bytes)
> * +--------+----------------+      +----------------+
> * | Length | Actual Content |----->| Actual Content |
> * | 0x000C | "HELLO, WORLD" |      | "HELLO, WORLD" |
> * +--------+----------------+      +----------------+
> * </pre>
> ```

------



> ```html
> *
> * <h3>2 bytes length field at offset 0, do not strip header, the length field
> *     represents the length of the whole message</h3>
> *
> * In most cases, the length field represents the length of the message body
> * only, as shown in the previous examples.  However, in some protocols, the
> * length field represents the length of the whole message, including the
> * message header.  In such a case, we specify a non-zero
> * <tt>lengthAdjustment</tt>.  Because the length value in this example message
> * is always greater than the body length by <tt>2</tt>, we specify <tt>-2</tt>
> * as <tt>lengthAdjustment</tt> for compensation.
> * <pre>
> * lengthFieldOffset   =  0
> * lengthFieldLength   =  2
> * <b>lengthAdjustment</b>    = <b>-2</b> (= the length of the Length field)
> * initialBytesToStrip =  0
> *
> * BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
> * +--------+----------------+      +--------+----------------+
> * | Length | Actual Content |----->| Length | Actual Content |
> * | 0x000E | "HELLO, WORLD" |      | 0x000E | "HELLO, WORLD" |
> * +--------+----------------+      +--------+----------------+
> * </pre>
> ```



> 例子三: 2字节长度域偏移量为0，不去头，长度域的值代表了整个消息的长度。
>
> 在大多数情况下，长度域仅仅表示消息体的长度，就像之前的例子一样。然而，在一些协议中，长度域代表了整个消息的长度，包含消息头。在这样的情况下，我们指定一个负的**lengthAdjustment**。因为长度的值通常要比消息体的长度大**2**字节，我们为该值指定为**-2**。
>
> ```html
> * lengthFieldOffset   =  0
> * lengthFieldLength   =  2
> * <b>lengthAdjustment</b>    = <b>-2</b> (= 长度域的长度)
> * initialBytesToStrip =  0
> *
> * BEFORE DECODE (14 bytes)         AFTER DECODE (14 bytes)
> * +--------+----------------+      +--------+----------------+
> * | Length | Actual Content |----->| Length | Actual Content |
> * | 0x000E | "HELLO, WORLD" |      | 0x000E | "HELLO, WORLD" |
> * +--------+----------------+      +--------+----------------+
> * </pre>
> ```

------

> ```
> * <h3>3 bytes length field at the end of 5 bytes header, do not strip header</h3>
> *
> * The following message is a simple variation of the first example.  An extra
> * header value is prepended to the message.  <tt>lengthAdjustment</tt> is zero
> * again because the decoder always takes the length of the prepended data into
> * account during frame length calculation.
> * <pre>
> * <b>lengthFieldOffset</b>   = <b>2</b> (= the length of Header 1)
> * <b>lengthFieldLength</b>   = <b>3</b>
> * lengthAdjustment    = 0
> * initialBytesToStrip = 0
> *
> * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
> * +----------+----------+----------------+      +----------+----------+----------------+
> * | Header 1 |  Length  | Actual Content |----->| Header 1 |  Length  | Actual Content |
> * |  0xCAFE  | 0x00000C | "HELLO, WORLD" |      |  0xCAFE  | 0x00000C | "HELLO, WORLD" |
> * +----------+----------+----------------+      +----------+----------+----------------+
> * </pre>
> *
> ```



> 例子四: 3字节长度域在5字节的消息头最后，不去头
>
> 下面的消息是第一个例子的小变种。消息添加了一个额外的头部。**lengthAdjustment**还是0，因为解码器总是将添加的数据计算在内。
>
> ```html
> * <pre>
> * <b>lengthFieldOffset</b>   = <b>2</b> (= the length of Header 1)
> * <b>lengthFieldLength</b>   = <b>3</b>
> * lengthAdjustment    = 0
> * initialBytesToStrip = 0
> *
> * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
> * +----------+----------+----------------+      +----------+----------+----------------+
> * | Header 1 |  Length  | Actual Content |----->| Header 1 |  Length  | Actual Content |
> * |  0xCAFE  | 0x00000C | "HELLO, WORLD" |      |  0xCAFE  | 0x00000C | "HELLO, WORLD" |
> * +----------+----------+----------------+      +----------+----------+----------------+
> * </pre>
> ```

------



> ```html
> * <h3>3 bytes length field at the beginning of 5 bytes header, do not strip header</h3>
> *
> * This is an advanced example that shows the case where there is an extra
> * header between the length field and the message body.  You have to specify a
> * positive <tt>lengthAdjustment</tt> so that the decoder counts the extra
> * header into the frame length calculation.
> * <pre>
> * lengthFieldOffset   = 0
> * lengthFieldLength   = 3
> * <b>lengthAdjustment</b>    = <b>2</b> (= the length of Header 1)
> * initialBytesToStrip = 0
> *
> * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
> * +----------+----------+----------------+      +----------+----------+----------------+
> * |  Length  | Header 1 | Actual Content |----->|  Length  | Header 1 | Actual Content |
> * | 0x00000C |  0xCAFE  | "HELLO, WORLD" |      | 0x00000C |  0xCAFE  | "HELLO, WORLD" |
> * +----------+----------+----------------+      +----------+----------+----------------+
> * </pre>
> ```



> 例子五: 3字节的长度域在5字节头的开头，不去头
>
> 这是一个进阶的例子，在长度域及消息体之间有其它的头信息。你不得不指定一个正的**lengthAdjustment**使得解码器在计算帧长度时算上额外的头部。
>
> ```html
> * <pre>
> * lengthFieldOffset   = 0
> * lengthFieldLength   = 3
> * <b>lengthAdjustment</b>    = <b>2</b> (= 额外头部的长度)
> * initialBytesToStrip = 0
> *
> * BEFORE DECODE (17 bytes)                      AFTER DECODE (17 bytes)
> * +----------+----------+----------------+      +----------+----------+----------------+
> * |  Length  | Header 1 | Actual Content |----->|  Length  | Header 1 | Actual Content |
> * | 0x00000C |  0xCAFE  | "HELLO, WORLD" |      | 0x00000C |  0xCAFE  | "HELLO, WORLD" |
> * +----------+----------+----------------+      +----------+----------+----------------+
> * </pre>
> ```



------



> ```html
> *
> * <h3>2 bytes length field at offset 1 in the middle of 4 bytes header,
> *     strip the first header field and the length field</h3>
> *
> * This is a combination of all the examples above.  There are the prepended
> * header before the length field and the extra header after the length field.
> * The prepended header affects the <tt>lengthFieldOffset</tt> and the extra
> * header affects the <tt>lengthAdjustment</tt>.  We also specified a non-zero
> * <tt>initialBytesToStrip</tt> to strip the length field and the prepended
> * header from the frame.  If you don't want to strip the prepended header, you
> * could specify <tt>0</tt> for <tt>initialBytesToSkip</tt>.
> * <pre>
> * lengthFieldOffset   = 1 (= the length of HDR1)
> * lengthFieldLength   = 2
> * <b>lengthAdjustment</b>    = <b>1</b> (= the length of HDR2)
> * <b>initialBytesToStrip</b> = <b>3</b> (= the length of HDR1 + LEN)
> *
> * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
> * +------+--------+------+----------------+      +------+----------------+
> * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
> * | 0xCA | 0x000C | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
> * +------+--------+------+----------------+      +------+----------------+
> * </pre>
> *
> ```



> 例子六: 2字节长度域偏移量为1，在4字节头部的中间，去掉第一个和长度头部域。
>
> 这是一个前面全部例子的集合。长度域前后都有额外的信息。附加的头部信息影响了**lengthFieldOffset**的值，额外的头信息影响了**lengthAdjustment**的值。我们还需要指定一个负的**initialBytesToStrip**以去掉附加的和长度域头部。如果你不想去掉附加的头部域，你可以将**initialBytesToSkip**设置为0。
>
> ```html
> * <pre>
> * lengthFieldOffset   = 1 (= the length of HDR1)
> * lengthFieldLength   = 2
> * <b>lengthAdjustment</b>    = <b>1</b> (= the length of HDR2)
> * <b>initialBytesToStrip</b> = <b>3</b> (= the length of HDR1 + LEN)
> *
> * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
> * +------+--------+------+----------------+      +------+----------------+
> * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
> * | 0xCA | 0x000C | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
> * +------+--------+------+----------------+      +------+----------------+
> * </pre>
> ```



------

> ```
> *
> * <h3>2 bytes length field at offset 1 in the middle of 4 bytes header,
> *     strip the first header field and the length field, the length field
> *     represents the length of the whole message</h3>
> *
> * Let's give another twist to the previous example.  The only difference from
> * the previous example is that the length field represents the length of the
> * whole message instead of the message body, just like the third example.
> * We have to count the length of HDR1 and Length into <tt>lengthAdjustment</tt>.
> * Please note that we don't need to take the length of HDR2 into account
> * because the length field already includes the whole header length.
> * <pre>
> * lengthFieldOffset   =  1
> * lengthFieldLength   =  2
> * <b>lengthAdjustment</b>    = <b>-3</b> (= the length of HDR1 + LEN, negative)
> * <b>initialBytesToStrip</b> = <b> 3</b>
> *
> * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
> * +------+--------+------+----------------+      +------+----------------+
> * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
> * | 0xCA | 0x0010 | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
> * +------+--------+------+----------------+      +------+----------------+
> * </pre>
> ```



> 例子七: 2字节长度域偏移量为1，在4字节头部的中间，去掉第一个头部域和长度域，长度域代表了整个信息的长度。
>
> 这个例子是上个例子的小变形。唯一不同就是长度域的值表示了整个信息的长度。我们不得不将第一个头部和长度域计算到**lengthAdjustment**。请记住我们不需要计算第二个头部域的长度，因为长度域已经包含了整个头部的长度。
>
> ```
> * <pre>
> * lengthFieldOffset   =  1
> * lengthFieldLength   =  2
> * <b>lengthAdjustment</b>    = <b>-3</b> (= the length of HDR1 + LEN, negative)
> * <b>initialBytesToStrip</b> = <b> 3</b>
> *
> * BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)
> * +------+--------+------+----------------+      +------+----------------+
> * | HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
> * | 0xCA | 0x0010 | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
> * +------+--------+------+----------------+      +------+----------------+
> * </pre>
> ```