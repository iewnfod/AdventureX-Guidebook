# hackpack-researchkit

需要 Mac 和 XCode 9+

在这个 iOS hackpack 中，我们将使用 Swift 4 和 XCode 9 来解决 Apple 的 ResearchKit 和 Healthkit 的问题。功能包括创建一个同意书以从用户那里请求同意，分发带有不同类型问题的调查表单，以及 iOS 应用程序从 iPhone 的 Health 应用程序中收集数据的访问点。

--------------------------------------------------------------------------------

首先，让我们创建一个标准的 XCode 项目。我们想通过以下步骤将 ResearchKit 和 HealthKit 嵌入到我们的 iOS 应用程序中。

**第一步：** 请注册一个开发者帐户（用于HealthKit使用），大约需要5-10分钟。[https://developer.apple.com/programs/] 克隆此存储库并按照步骤进行操作！使用 [master] 获取完整的解决方案和 [starterkit] 来填写代码。

**第二步：** 要下载最新版本的ResearchKit，并在终端中键入 *git clone -b stable [https://github.com/ResearchKit/ResearchKit.git](https://github.com/ResearchKit/ResearchKit)*。然后，进入带有 .xcodeprj 扩展名的文件，并通过运行ResearchKit框架来构建项目。

**第三步：** 将 *ResearchKit.xcodeproj* 拖到当前 iOS 项目中，如果需要，复制项目。如果在拖入后看不到 ResearchKit 旁边有箭头，请等待加载，或关闭并重新打开项目。

![addresearch](https://cloud.githubusercontent.com/assets/6894456/21839806/9a6d44d8-d78e-11e6-8c07-640776371eb2.png)

**第四步：** 转到项目的 *General* 设置，滚动到 *Embedded Binaries*。单击 + 按钮并添加 ResearchKit。

![embed](https://cloud.githubusercontent.com/assets/6894456/21839842/d9c05b98-d78e-11e6-9857-5e3a72ee917d.png)

**第五步：** 要使用 HealthKit，请转到 *Capabilities* 设置，滚动到底部以打开 HealthKit 访问，它应该会自动添加到您的项目中。

![healthkit](https://cloud.githubusercontent.com/assets/6894456/21839857/f3322408-d78e-11e6-8f2c-910e4bee392a.png)

**第六步：** 在您的 info.plist 中，右键单击以打开源代码，然后粘贴：

```swift
    <key>NSHealthShareUsageDescription</key>
    <string>Need to share healthkit information</string>
    <key>NSHealthUpdateUsageDescription</key>
    <string>Need healthkit to track steps</string>
```

![infoplist](https://cloud.githubusercontent.com/assets/6894456/21839876/0c9ce586-d78f-11e6-855a-0b214b4c08a4.png)

**第七步：**（可选）如果您在 XCode 模拟器中测试代码，则必须从 Health App 模拟数据，以便从 HealthStore 获取结果。为此，请运行 iPhone 模拟器，单击硬件选项卡的主页按钮，然后导航到 Health 应用程序。点击要查看的类别，然后使用 + 按钮添加数据。例如，要获取步数，转到 *活动*，添加不同天数的运动量。

以上步骤为您的 Xcode 项目安装了 ResearchKit 和 HealthKit！

--------------------------------------------------------------------------------

*master* 分支包含具有 ResearchKit 同意和调查表单以及 HealthKit 访问和步数计数的完整代码。*starterkit* 分支包含具有注释和适用于项目各部分的自行添加/自定义的代码部分。

**Main.storyboard** 创建导航控制器，按钮链接到同意、调查和步数计数的特定功能，用于描述用途的文本框以及用于 HealthKit 数据的单独视图控制器。按钮和文本框对齐以适应每个 iPhone 产品的规格。

**FirstViewController.swift** 将同意和调查按钮链接到其特定功能，该功能呈现一个具有特定任务（例如，ConsentTask、SurveyTasks）的新taskViewController。taskViewController 继承自 ORKTaskViewController，以在操作完成后关闭 viewController。

**ConsentDocument.swift** 和 **ConsentTasks.swift** 创建了一个同意书，要求用户同意研究。**ConsentTasks.swift** 创建了一个 ORKOrderedTask 来逐步执行创建的文档。**ConsentDocument.swift** 使用特定问题创建了一个 ORKConsentDocument，用户可以同意，并在表单中包含签名部分。预建同意事项的示例如下：

```Swift
let consentSectionTypes: [ORKConsentSectionType] = [
        .overview,
        .dataGathering,
        .privacy,
        .dataUse,
        .studySurvey,
        .studyTasks,
        .withdrawing
    ]
```

**SurveyTask.swift** 创建了一个供用户填写的简单调查表单。它也是一个 ORKOrderedTask，初始化了问题的格式和详细信息，以及一个用于逐步执行的摘要步骤。示例问题如下：

```Swift
    let nameAnswerFormat = ORKTextAnswerFormat(maximumLength: 20)
    nameAnswerFormat.multipleLines = false
    let nameQuestionStepTitle = "What is your name?"
    let nameQuestionStep = ORKQuestionStep(identifier: "QuestionStep", title: nameQuestionStepTitle, answer: nameAnswerFormat)
    steps += [nameQuestionStep]
```

**HealthKitViewController.swift** 请求 HealthKit 访问并获取用户过去七天的步数。请记住，HealthKit 访问表单将仅出现一次，因为用户只需要一次接受协议。请求信息授权的示例代码如下：

```Swift
        if HKHealthStore.isHealthDataAvailable() {
            let stepsCount = NSSet(object: HKQuantityType.quantityType(forIdentifier: HKQuantityTypeIdentifier.stepCount))
            let sharedObjects = NSSet(objects: HKQuantityType.quantityType(forIdentifier: HKQuantityTypeIdentifier.height),HKQuantityType.quantityType(forIdentifier: HKQuantityTypeIdentifier.bodyMass))
            
            healthStore.requestAuthorization(toShare: sharedObjects as? Set<HKSampleType>, read: stepsCount as? Set<HKObjectType>, completion: { (success, err) in
                self.getStepCount(sender: self)
            })
            
        } 
```
创建用于 HealthStor 执行的查询的示例代码如下：
```Swift
        let predicate = HKQuery.predicateForSamples(withStart: dates, end: Date(), options: [])
        let query = HKSampleQuery(sampleType: type!, predicate: predicate, limit: 0, sortDescriptors: nil) {
            query, results, error in
            var steps: Double = 0
            var allSteps = [Double]()
            if let myResults = results {
                for result in myResults as! [HKQuantitySample] {
                    print(myResults)
                    steps += result.quantity.doubleValue(for: HKUnit.count())
                    allSteps.append(result.quantity.doubleValue(for: HKUnit.count()))
                }
            }
            completion(steps, allSteps, error as NSError?)
            
        }
        
        // Executes query through healthstore
        healthStore.execute(query)
```

这些是使用 Swift 4 和 XCode 9 与 ResearchKit 和 HealthKit 的一些功能。您可以轻松自定义这些项目，并保存信息以在iOS应用程序的其他部分中使用，以创建一个医疗保健应用！