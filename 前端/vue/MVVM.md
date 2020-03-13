# MVVM
MVVM(Model-View-ViewModel)是一个**软件架构设计模式**，是一种简化用户界面的**事件驱动编程方式**  
MVVM源自于经典的MVC模式，MVVM促进了前端开发与后端业务逻辑的分离，MVVM的核心是ViewModel层，它就像一个**中转站**，负责转换Model中的数据对象来让数据变得更容易管理和使用，该层向上与视图层进行双向数据绑定，向下与Model层通过ajax、json等技术进行接口请求来进行数据交互  
## View层
视图层，也就是用户界面。前端主要由 HTML 和 CSS 来构建
## Model层
数据模型，泛指后端进行的各种业务逻辑处理和数据操控，对于前端来说就是后端提供的api接口
## ViewModel层
前端开发人员组织生成和维护的视图数据层。在这一层，前端开发者对从后端获取的 Model 数据进行转换处理，做二次封装，以生成符合 View 层使用预期的视图数据模型。  
ViewModel所封装出来的数据模型包括视图的**状态**和**行为**两部分。而 Model 层的数据模型是只包含状态的，比如页面的这一块展示什么，而页面加载进来时发生什么，点击这一块发生什么，这一块滚动时发生什么这些都属于视图行为（交互），视图状态和行为都封装在了 ViewModel 里。这样的封装使得 ViewModel 可以完整地去描述 View 层
# MVC、MVP、MVVM的区别
MVC、MVP和MVVM都是用来解决**界面呈现和逻辑代码分离**而出现的模式
## MVC优点 Model View Controller
* 耦合性低
* 开发速度快
* 可维护性高
## MVC缺点
MVC的设计是从Model出发，而没有考虑到View端的复杂性，这样导致的问题是**Model难以符合复杂多变的View端变化**
## MVP Model iview Presenter
MVP里，Presenter**完全把Model和View进行了分离，修改视图而不影响模型**，主要的程序逻辑在Presenter里实现，它将UI界面接口化
## MVVM
MVVM模式中，一个ViewModel和一个View匹配，它没有MVP中的IView接口，而是完全的和View绑定，所有View中的修改变化，都会自动更新到ViewModel中，同时ViewModel的任何变化也会自动同步到View上显示  
MVVM比MVP更升级一步，在MVP中，V是接口IView，解决对于界面UI的耦合；而MVVM干脆直接使用ViewModel和UI的无缝结合，ViewModel直接就能代表UI