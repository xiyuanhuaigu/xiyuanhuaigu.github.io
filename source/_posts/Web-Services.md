---
title: "Web Services"
date: 2022-05-18 19:03:41
---
## 简介：

​ Web Service是一个[平台](https://baike.baidu.com/item/%E5%B9%B3%E5%8F%B0/1064049)独立的，低耦合的，自包含的、基于可[编程](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B)的web的应用程序，可使用开放的[XML](https://baike.baidu.com/item/XML)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80/6805073)下的一个子集）[标准](https://baike.baidu.com/item/%E6%A0%87%E5%87%86/219665)来[描述](https://baike.baidu.com/item/%E6%8F%8F%E8%BF%B0/8928757)、发布、发现、协调和配置这些应用程序，用于开发分布式的交互操作的[应用程序](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F/5985445)。 [1]

​ Web Service技术， 能使得运行在不同机器上的不同应用无须借助附加的、专门的第三方软件或硬件， 就可相互交换数据或集成。依据Web Service规范实施的应用之间， 无论它们所使用的语言、 平台或内部协议是什么， 都可以相互交换数据。Web Service是自描述、 自包含的可用网络模块， 可以执行具体的业务功能。Web Service也很容易部署， 因为它们基于一些常规的产业标准以及已有的一些技术，诸如[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80/6805073)下的子集[XML](https://baike.baidu.com/item/XML)、HTTP。Web Service减少了应用接口的花费。Web Service为整个企业甚至多个组织之间的业务流程的集成提供了一个通用机制。

## 支持：

Web Service平台需要一套协议来实现[分布式应用程序](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)的创建。任何平台都有它的[数据表示](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E8%A1%A8%E7%A4%BA)方法和类型系统。要实现[互操作性](https://baike.baidu.com/item/%E4%BA%92%E6%93%8D%E4%BD%9C%E6%80%A7)，Web Service平台必须提供一套标准的类型系统，用于沟通不同平台、编程语言和[组件模型](https://baike.baidu.com/item/%E7%BB%84%E4%BB%B6%E6%A8%A1%E5%9E%8B)中的不同类型系统。这些协议有：

**XML和XSD**

可扩展的[标记语言](https://baike.baidu.com/item/%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）是Web Service平台中表示数据的基本格式。除了易于建立和易于分析外，XML主要的优点在于它既与平台无关，又与厂商无关。XML是由万维网协会(W3C)创建，W3C制定的XML SchemaXSD　定义了一套标准的[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)，并给出了一种语言来扩展这套[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)。

Web Service平台是用XSD来作为[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)系统的。当你用某种语言如VB. NET或C#　来构造一个Web Service时，为了符合Web Service标准，所有你使用的[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)都必须被转换为XSD类型。如想让它使用在不同平台和不同[软件](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6)的不同组织间传递，还需要用某种东西将它包装起来。这种东西就是一种协议，如 SOAP。

**SOAP**

SOAP即[简单对象访问协议](https://baike.baidu.com/item/%E7%AE%80%E5%8D%95%E5%AF%B9%E8%B1%A1%E8%AE%BF%E9%97%AE%E5%8D%8F%E8%AE%AE)(Simple Object Access Protocol)，它是用于交换[XML](https://baike.baidu.com/item/XML)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）编码信息的轻量级协议。它有三个主要方面：XML-envelope为描述信息内容和如何处理内容定义了框架，将程序对象编码成为XML对象的规则，执行[远程过程调用](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8)(RPC)的约定。SOAP可以运行在任何其他[传输协议](https://baike.baidu.com/item/%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)上。例如，你可以使用 SMTP，即因特网[电子邮件协议](https://baike.baidu.com/item/%E7%94%B5%E5%AD%90%E9%82%AE%E4%BB%B6%E5%8D%8F%E8%AE%AE)来传递SOAP消息，这可是很有诱惑力的。在[传输层](https://baike.baidu.com/item/%E4%BC%A0%E8%BE%93%E5%B1%82)之间的头是不同的，但XML有效负载保持相同。

Web Service 希望实现不同的系统之间能够用“[软件](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6)-软件对话”的方式相互调用，打破了软件应用、网站和各种设备之间的格格不入的状态，实现“基于Web无缝集成”的目标。

**WSDL**

Web Service描述语言WSDL　就是用机器能阅读的方式提供的一个正式描述文档而基于[XML](https://baike.baidu.com/item/XML)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）的语言，用于描述Web Service及其函数、参数和返回值。因为是基于XML的，所以WSDL既是机器可阅读的，又是人可阅读的。

**UDDI**

UDDI 的目的是为电子商务建立标准；UDDI是一套基于Web的、分布式的、为Web Service提供的、信息注册中心的实现标准规范，同时也包含一组使企业能将自身提供的Web Service注册，以使别的企业能够发现的访问协议的实现标准。

**调用RPC与消息传递**

Web Service本身其实是在实现应用程序间的通信。我们有两种应用程序通信的方法：RPC[远程过程调用](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8)　和消息传递。使用RPC的时候，[客户端](https://baike.baidu.com/item/%E5%AE%A2%E6%88%B7%E7%AB%AF)的概念是调用服务器上的远程过程，通常方式为实例化一个远程对象并调用其方法和属性。RPC系统试图达到一种位置上的透明性：服务器暴露出远程对象的接口，而[客户端](https://baike.baidu.com/item/%E5%AE%A2%E6%88%B7%E7%AB%AF)就好像在本地使用的这些对象的接口一样，这样就隐藏了底层的信息，客户端也就根本不需要知道对象是在哪台机器上。

## 应用：

Web service到底是什么；在什么情况下你应该使用Web service。

研究一下当前的[应用程序开发](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E5%BC%80%E5%8F%91)，你会发现一个绝对的倾向：人们开始偏爱基于[浏览器](https://baike.baidu.com/item/%E6%B5%8F%E8%A7%88%E5%99%A8)的客户端应用程序。这当然不是因为客户端能够提供更好的用户界面，而是因为它能够避免花在[桌面应用程序](https://baike.baidu.com/item/%E6%A1%8C%E9%9D%A2%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)发布上的高成本。发布[桌面应用程序](https://baike.baidu.com/item/%E6%A1%8C%E9%9D%A2%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)成本很高，一半是因为应用程序安装和配置的问题，另一半是因为客户端和服务器之间通信的问题。

传统的Windows客户应用程序使用DCOM来与服务器进行通信和调用远程对象。配置好DCOM使其在一个大型的网络中正常工作将是一个极富挑战性的工作，同时也是许多IT工程师的噩梦。事实上，许多IT工程师宁愿忍受[浏览器](https://baike.baidu.com/item/%E6%B5%8F%E8%A7%88%E5%99%A8)所带来的功能限制，也不愿在局域网上去运行一个DCOM。在我看来，结果就是一个发布容易，但开发难度大而且用户界面极其受限的应用程序。极端的说，就是你花了更多的资金和时间，却开发出从用户看来功能更弱的应用程序。不信？问问你的会计师对新的基于浏览器的[会计软件](https://baike.baidu.com/item/%E4%BC%9A%E8%AE%A1%E8%BD%AF%E4%BB%B6)有什么想法：绝大多数商用程序用户希望使用更加友好的Windows用户界面。

关于[客户端](https://baike.baidu.com/item/%E5%AE%A2%E6%88%B7%E7%AB%AF)与服务器的通信问题，一个完美的解决方法是使用HTTP协议来通信。这是因为任何运行[Web浏览器](https://baike.baidu.com/item/Web%E6%B5%8F%E8%A7%88%E5%99%A8)的机器都在使用HTTP协议。同时，当前许多[防火墙](https://baike.baidu.com/item/%E9%98%B2%E7%81%AB%E5%A2%99)也配置为只允许HTTP连接。

许多商用程序还面临另一个问题，那就是与其他程序的[互操作性](https://baike.baidu.com/item/%E4%BA%92%E6%93%8D%E4%BD%9C%E6%80%A7)。如果所有的应用程序都是使用COM或.NET语言写的，并且都运行在Windows平台上，那就天下太平了。然而，事实上大多数商业数据仍然在大型主机上以非关系文件(VSAM)的形式存放，并由COBOL语言编写的大型机程序访问。而且，还有很多商用程序继续在使用C++、Java、Visual Basic和其他各种各样的语言编写。除了最简单的程序之外，所有的应用程序都需要与运行在其他异构平台上的应用程序集成并进行数据交换。这样的任务通常都是由特殊的方法，如[文件传输](https://baike.baidu.com/item/%E6%96%87%E4%BB%B6%E4%BC%A0%E8%BE%93)和分析，[消息队列](https://baike.baidu.com/item/%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97)，还有仅适用于某些情况的的API，如IBM的”高级程序到程序交流(APPC)”等来完成的。在以前，没有一个应用程序通信标准，是独立于平台、组建模型和[编程语言](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80)的。只有通过Web Service，[客户端](https://baike.baidu.com/item/%E5%AE%A2%E6%88%B7%E7%AB%AF)和服务器才能够自由的用HTTP进行通信，不论两个程序的平台和[编程语言](https://baike.baidu.com/item/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80)是什么。

**什么是Web Service**

对这个问题，我们至少有两种答案。从表面上看，Web service 就是一个应用程序，它向外界暴露出一个能够通过Web进行调用的API。这就是说，你能够用编程的方法通过Web来调用这个应用程序。我们把调用这个Web service 的应用程序叫做客户。例如，你想创建一个Web service ，它的作用是返回当前的天气情况。那么你可以建立一个ASP页面，它接受邮政编码作为查询字符串，然后返回一个由逗号隔开的字符串，包含了当前的气温和天气。要调用这个ASP页面，[客户端](https://baike.baidu.com/item/%E5%AE%A2%E6%88%B7%E7%AB%AF)需要发送下面的这个HTTP GET

返回的数据就应该是这样：

这个ASP页面就应该可以算作是Web service 了。因为它基于HTTP GET请求，暴露出了一个可以通过Web调用的API。当然，Web service 还有更多的东西。

下面是对Web service 更精确的解释： Web services是建立可互操作的[分布式应用程序](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)的新平台。作为一个Windows程序员，你可能已经用COM或DCOM建立过基于组件的[分布式应用程序](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)。COM是一个非常好的组件技术，但是我们也很容易举出COM并不能满足要求的情况。

Web service平台是一套标准，它定义了应用程序如何在Web上实现[互操作性](https://baike.baidu.com/item/%E4%BA%92%E6%93%8D%E4%BD%9C%E6%80%A7)。你可以用任何你喜欢的语言，在任何你喜欢的平台上写Web service ，只要我们可以通过Web service标准对这些服务进行查询和访问。

**新平台**

Web service平台需要一套协议来实现[分布式应用程序](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)的创建。Web service平台必须提供一套标准的类型系统，用于沟通不同平台、编程语言和[组件模型](https://baike.baidu.com/item/%E7%BB%84%E4%BB%B6%E6%A8%A1%E5%9E%8B)中的不同类型系统。在传统的[分布式系统](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E7%B3%BB%E7%BB%9F)中，基于界面(interface)的平台提供了一些方法来描述界面、方法和参数（译注：如COM和COBAR中的IDL语言）。同样的，Web service平台也必须提供一种标准来描述Web service，让客户可以得到足够的信息来调用这个Web service。最后，我们还必须有一种方法来对这个Web service进行远程调用。这种方法实际是一种[远程过程调用](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8)协议(RPC)。为了达到[互操作性](https://baike.baidu.com/item/%E4%BA%92%E6%93%8D%E4%BD%9C%E6%80%A7)，这种RPC协议还必须与平台和编程语言无关。下面几个小节就简要介绍了组成Web service平台的这三个技术。

**XML和XSD**

可扩展的[标记语言](https://baike.baidu.com/item/%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）是Web service平台中表示数据的基本格式。除了易于建立和易于分析外，XML主要的优点在于它既是平台无关的，又是厂商无关的。无关性是比技术优越性更重要的：[软件](https://baike.baidu.com/item/%E8%BD%AF%E4%BB%B6)厂商是不会选择一个由竞争对手所发明的技术的。

XML解决了[数据表示](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E8%A1%A8%E7%A4%BA)的问题，但它没有定义一套标准的[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)，更没有说怎么去扩展这套数据类型。例如，整形数到底代表什么？16位，32位，还是64位？这些细节对实现[互操作性](https://baike.baidu.com/item/%E4%BA%92%E6%93%8D%E4%BD%9C%E6%80%A7)都是很重要的。W3C制定的XML Schema(XSD)就是专门解决这个问题的一套标准。它定义了一套标准的数据类型，并给出了一种语言来扩展这套数据类型。Web service平台就是用XSD来作为其[数据类型](https://baike.baidu.com/item/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)系统的。当你用某种语言(如VB. NET或C#)来构造一个Web service时，为了符合Web service标准，所有你使用的数据类型都必须被转换为XSD类型。你用的工具可能已经自动帮你完成了这个转换，但你很可能会根据你的需要修改一下转换过程。

**SOAP**

Web service建好以后，你或者其他人就会去调用它。[简单对象访问协议](https://baike.baidu.com/item/%E7%AE%80%E5%8D%95%E5%AF%B9%E8%B1%A1%E8%AE%BF%E9%97%AE%E5%8D%8F%E8%AE%AE)(SOAP)提供了标准的RPC方法来调用Web service。实际上，SOAP在这里有点用词不当：它意味着下面的Web service是以对象的方式表示的，但事实并不一定如此：你完全可以把你的Web service写成一系列的C函数，并仍然使用SOAP进行调用。SOAP规范定义了SOAP消息的格式，以及怎样通过HTTP协议来使用SOAP。SOAP也是基于[XML](https://baike.baidu.com/item/XML)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）和XSD的，XML是SOAP的数据编码方式。

**WSDL**

你会怎样向别人介绍你的Web service有什么功能，以及每个[函数调用](https://baike.baidu.com/item/%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8)时的参数呢？你可能会自己写一套文档，你甚至可能会口头上告诉需要使用你的Web service的人。这些非正式的方法至少都有一个严重的问题：当程序员坐到电脑前，想要使用你的Web service的时候，他们的工具(如Visual Studio)无法给他们提供任何帮助，因为这些工具根本就不了解你的Web service。

解决方法是：用机器能阅读的方式提供一个正式的描述文档。Web service描述语言([WSDL](https://baike.baidu.com/item/WSDL))就是这样一个基于[XML](https://baike.baidu.com/item/XML)（[标准通用标记语言](https://baike.baidu.com/item/%E6%A0%87%E5%87%86%E9%80%9A%E7%94%A8%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80)下的一个子集）的语言，用于描述Web service及其函数、[参数](https://baike.baidu.com/item/%E5%8F%82%E6%95%B0)和返回值。[WSDL](https://baike.baidu.com/item/WSDL)既是机器可阅读的，又是人可阅读的，这将是一个很大的好处。一些最新的开发工具既能根据你的Web service生成WSDL文档，又能导入WSDL文档，生成调用相应Web service的代码。

**UDDI**

Universal Description, Discovery and Integration

为加速Web Service的推广、加强Web Service的互操作能力而推出的一个计划,基于标准的服务描述和发现的规范(specification)。

以[资源共享](https://baike.baidu.com/item/%E8%B5%84%E6%BA%90%E5%85%B1%E4%BA%AB)的方式由多个运作者一起以Web Service的形式运作UDDI商业注册中心。

UDDI计划的核心组件是UDDI商业注册，它使用[XML文档](https://baike.baidu.com/item/XML%E6%96%87%E6%A1%A3)来描述企业及其提供的Web Service。

UDDI商业注册提供三种信息：

White Page包含地址、联系方法、已知的[企业标识](https://baike.baidu.com/item/%E4%BC%81%E4%B8%9A%E6%A0%87%E8%AF%86)。

Yellow Page包含基于标准分类法的行业类别。

Green Page包含关于该企业所提供的Web Service的技术信息，其形式可能是指向文件或[URL](https://baike.baidu.com/item/URL)的指针，而这些文件或URL是为服务发现机制服务的。

**Web Service开发实例**

1. 利用WebService实现数据添加
2. 利用WebService实现数据删除
3. 利用WebService给手机发短信 [2]

**适合使用Web Service的情况**

1. 跨越[防火墙](https://baike.baidu.com/item/%E9%98%B2%E7%81%AB%E5%A2%99/52767)；
2. 应用程序集成；
3. [B2B](https://baike.baidu.com/item/B2B)集成；
4. 软件重用

**不适合使用Web服务的情况**

1. 单机[应用程序](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)；
2. [局域网](https://baike.baidu.com/item/%E5%B1%80%E5%9F%9F%E7%BD%91)上的同构应用程序
