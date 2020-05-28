---
<span data-ttu-id="1aa88-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1aa88-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1aa88-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1aa88-102">'Blazor'</span></span>
- <span data-ttu-id="1aa88-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1aa88-103">'Identity'</span></span>
- <span data-ttu-id="1aa88-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1aa88-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="1aa88-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1aa88-105">'Razor'</span></span>
- <span data-ttu-id="1aa88-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="1aa88-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="1aa88-107">Yük/stres testini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1aa88-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="1aa88-108">Yük testi ve stres testi, Web uygulamasının performansı ve ölçeklenebilir olmasını sağlamak için önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1aa88-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="1aa88-109">Genellikle benzer testleri paylaşsalar bile hedefleri farklıdır.</span><span class="sxs-lookup"><span data-stu-id="1aa88-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="1aa88-110">**Yük testleri**: uygulamanın, yanıt hedefini karşılarken belirli bir senaryo için belirtilen bir kullanıcı yükünü işleyemeyeceğini test edin.</span><span class="sxs-lookup"><span data-stu-id="1aa88-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="1aa88-111">Uygulama normal koşullarda çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="1aa88-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="1aa88-112">**Stres testleri**: genellikle uzun bir süre boyunca yoğun koşullarda çalışırken uygulamanın kararlılığını test edin.</span><span class="sxs-lookup"><span data-stu-id="1aa88-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="1aa88-113">Testler, uygulamanın yoğun veya aşamalı olarak yükünü artırır ya da uygulamanın bilgi işlem kaynaklarını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="1aa88-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="1aa88-114">Stres testleri, stres kapsamındaki bir uygulamanın hatadan kurtulacağını ve düzgün biçimde beklenen davranışa geri döneceğini denetler.</span><span class="sxs-lookup"><span data-stu-id="1aa88-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="1aa88-115">Stres altında, uygulama normal koşullarda çalıştırılmamaları.</span><span class="sxs-lookup"><span data-stu-id="1aa88-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="1aa88-116">Visual Studio 2019, Visual Studio 'nun yük testi özellikleriyle son sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="1aa88-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="1aa88-117">Gelecekte yük testi araçları gerektiren müşteriler için Apache JMeter, Akamai CloudTest ve BlazeMeter gibi alternatif araçlar önerilir.</span><span class="sxs-lookup"><span data-stu-id="1aa88-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="1aa88-118">Daha fazla bilgi için bkz. [Visual Studio 2019 sürüm notları](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="1aa88-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="1aa88-119">Visual Studio araçları</span><span class="sxs-lookup"><span data-stu-id="1aa88-119">Visual Studio tools</span></span>

<span data-ttu-id="1aa88-120">Visual Studio, kullanıcıların Web performans ve yük testleri oluşturmalarına, geliştirmesine ve hata ayıklamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1aa88-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="1aa88-121">Bir Web tarayıcısında eylemleri kaydederek testler oluşturmak için bir seçenek mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="1aa88-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="1aa88-122">Visual Studio 2017 kullanarak yük testi projeleri oluşturma, yapılandırma ve çalıştırma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: yük testi projesi oluşturma](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="1aa88-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="1aa88-123">Yük testleri, şirket içinde çalışacak veya Azure DevOps kullanılarak bulutta çalıştırılacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="1aa88-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="1aa88-124">Üçüncü taraf araçları</span><span class="sxs-lookup"><span data-stu-id="1aa88-124">Third-party tools</span></span>

<span data-ttu-id="1aa88-125">Aşağıdaki listede, çeşitli özellik kümelerine sahip üçüncü taraf Web performans araçları yer almaktadır:</span><span class="sxs-lookup"><span data-stu-id="1aa88-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="1aa88-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="1aa88-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="1aa88-127">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="1aa88-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="1aa88-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="1aa88-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="1aa88-129">k6</span><span class="sxs-lookup"><span data-stu-id="1aa88-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="1aa88-130">Locust</span><span class="sxs-lookup"><span data-stu-id="1aa88-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="1aa88-131">Batı rüzgar Web dalgalanma</span><span class="sxs-lookup"><span data-stu-id="1aa88-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="1aa88-132">Netling</span><span class="sxs-lookup"><span data-stu-id="1aa88-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="1aa88-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="1aa88-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

