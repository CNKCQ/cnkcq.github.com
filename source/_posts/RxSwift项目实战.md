title: RxSwift --- 项目实战
date: 2016-09-20 9:58:12
tags: [iOS,博客]

### 前言
RxSwift  源自ReactiveX，它蕴含了深刻的FRP思想，这是一个崭新的世界，我也是慕名而来，本着好学的心态，叩开RxSwift的大门，来了之后才发现，这里人迹罕至，路上满是泥泞，坑坑洼洼，幸好遇到了RxExample, 从此便奉为圭皋，仔细研读，跟随者开拓者的足迹，我也进行了一次RxSwift之旅，其中酸甜苦辣，请亲自品尝。

### 装备
装备的选择对你趟坑过河的影响还是蛮大的，选好内裤，走起路来便可以雄赳赳气昂昂。 我选择的内裤便是 Moya、Alamofire、ObjectMapper 等，有了这些内裤，你便可以优雅的踩坑。

### 出发

作为一个swifter Alamofire 对你来说并不陌生，它的优雅与简洁没有多少库能出其左右，然而对于 Moya 你可能未必熟悉，不过你不熟悉也没关系，协议和扩展造就了他的优雅与平易近人，你可以轻松的用它来封装你的网络请求层，他与Alamofire 也是不离不弃.
如果想获取信息，那么你需这样做：
```bash
enum OSCIOService {
    case NewsList, NewBanner, TweetList, BlogList, EventList, EventBanner
    case Login(username: String, password: String)
    case FindUser(name: String)
    case Search(content: String)
}

extension OSCIOService: TargetType {

    var baseURL: NSURL {
//        return NSURL(string: "http://www.oschina.net/action/api")! //XML格式
        return NSURL(string: "http://www.oschina.net/action/apiv2")! //JSON格式
    }

    var path: String {
        switch self {
        case .NewsList:
            return "/news"
        case .NewBanner:
            return "/banner"
        case .TweetList:
            return "/tweet_list"
        case .BlogList:
            return "/blog_list"
        case .EventList:
            return "/event_list"
        case .EventBanner:
            return "/banner"
        case .Login( _, _):
            return "/login_validate"
        case .FindUser(_):
            return "/find_user"
        case .Search(_):
            return "/search_list"
        }
    }

    var method: Moya.Method {
        switch self {
        case .Login:
            return .POST
        default:
            return .GET
        }
    }

    var parameters: [String: AnyObject]? {
        switch self {
        case .Login(let username, let password):
            return ["username": username, "pwd": password]
        case .NewBanner:
            return ["catalog": 1]
        case .EventBanner:
            return ["catalog": 3]
        case .FindUser(let name):
            return ["name":name]
        default:
             return nil
        }
    }

    var sampleData: NSData {
        return "{}".dataUsingEncoding(NSUTF8StringEncoding)! // for test
    }

    var multipartBody: [MultipartFormData]? {
        return nil
    }
}
```
这样就可以封装好你的API了
既然我们来到的FRP的路上，那么我们应该也改改以前惯用的MVC了，MVVM 才是更优雅的姿势，那个问题来了，我们的viewModel 呢？别着急，请看下面：
```bash
class NewsViewModel {
    var provider: RxMoyaProvider<OSCIOService>
    var backgroundScheduler: OperationQueueScheduler!


    init() {
        let operationQueue = NSOperationQueue()
        backgroundScheduler = OperationQueueScheduler(operationQueue: operationQueue)
        self.provider = RxMoyaProvider<OSCIOService>()
    }

    func fetch() -> Observable<[NewsItem]?> {
        return Observable.create({ observer -> Disposable in
            self.provider.request(OSCIOService.NewsList) { response in
                switch response {
                case let .Success(response):
                    let result = Mapper<NewsRootClass>().map(String(data: response.data, encoding:  NSUTF8StringEncoding))
                    observer.on(Event.Next(result?.result?.items))
                case let .Failure(error):
                    observer.on(Event.Error(error))
                }
                observer.onCompleted()
            }
            return NopDisposable.instance
        })
    }
}
```
在viewModel中我提供了一个网络 Observable 以供外部订阅，将数据转化为可以订阅的Stream
那么这样我们就可以到 Controller 进行数据订阅了：
```bash
let viewModel = NewsViewModel()
        viewModel.fetch().subscribe(
            onNext: { entities in
                if let result = entities {
                    self.newsItems = result
                }
                log.info("你好")
            }, onError: { error in
                log.error("\(error)")
            }, onCompleted: {
                log.info("completed")
            }, onDisposed: {
                log.info("disposed")

        }).addDisposableTo(self.disposeBag)

```
当我们拿到了数据，我们就可以进行数据展示了，这与我们常规的数据展示不无差别
好了，由于文字功底有限，我就不赘述了

完整项目可移步：https://github.com/CNKCQ

相关资料：
https://mcxiaoke.gitbooks.io/rxdocs/content/
https://github.com/ReactiveX/RxSwift/blob/master/Documentation/GettingStarted.md
http://cocoadocs.org/docsets/RxCocoa/1.5/index.html
https://coderwall.com/p/vti_8w/rxswift-learning-resources
https://leon_lizi.gitbooks.io/rx-/content/observableyu_alamofire.html
http://www.oschina.net/openapi/docs/
