{
  title: "C#",
  description: "How to run Selenium tests on Sauce Labs using C#",
  category: 'Tutorials',
  index: 10,
  image: "/images/tutorials/csharp.png"
}

## Getting Started

You can download the premade project [here](https://github.com/saucelabs/sauce-dotnet-examples/archive/master.zip), or check it out [on Github](https://github.com/saucelabs/sauce-dotnet-examples/). You may need to enable Package Downloading for NuGet. You can do so by checking both boxes in the `Package Management` section of the `Tools > Options` dialog.

Alternatively, if you'd like to create your own project

1. Launch Visual Studio and Select File > New > Project
2. Select `Class Library`, give the solution a name and click `OK`
3. In the `Solution Explorer` right-click on `References` and select `Add Reference` or alternative you can skip steps 3-6 and install `MbUnit.framework` and `Selenium` with NuGet
4. Under the `Assemblies` section on the left, choose `Extensions`, select `MbUnit.dll` from the list and click `OK`
5. Add `using MbUnit.Framework;` to the using statements section in your class.
6. Repeat with steps 3-5 for `Gallio.dll`, `WebDriver.dll`, and `WebDriver.Support.dll`

If you're using the sample project you'll want to update `Constants.cs` to have your Sauce Labs account name and account key.

## Writing tests

The sample project includes some example tests in its root directory.
Lets look at the test suite in `GuineaPigTests.cs`.

The `_Init()` method initializes the browser testing environment by specifying the
browser, version, and platform to test, then creates a
`RemoteWebDriver` to run the tests remotely.

This test connects to Sauce Labs, runs commands
to remote-control a browser, and reports the results. It runs against several
browsers simultaneously, to demonstrate parallelized testing. The `RemoteWebDriver` is a [standard
Selenium
interface](http://selenium.googlecode.com/git/docs/api/dotnet/html/T_OpenQA_Selenium_Remote_RemoteWebDriver.htm),
so you can do anything that you could do with a
local Selenium test. The only code specific to Sauce Labs was the URL
that makes the test run using a browser on Sauce Labs' servers.

## Running tests

(If you downloaded the pre-canned project this is already done for you)

1. Right-click on the .csproj in the Solution Explorer and select `Properties`
2. Go to the `Debug` tab
3. Under `Start Action` select `Start an External Program`
4. Set the path to the Gallio Icarus GUI Runner. It will likely be in `C:\Program Files (x86)\Gallio\bin\Gallio.Icarus.exe`
    - If you don't have Gallio installed, you can download it [here](https://mb-unit.googlecode.com/files/GallioBundle-3.4.14.0.zip)
5. Set the command line arguments to the name of your DLL, In the case of the test project it is `SauceLabs.SeleniumExamples.dll`
6. Build the project and Debug the Solution (press the F5 key)

This launches the Gallio test GUI where you can run your tests. The DLL for this project should be preloaded. If it is not you can open it from the `File` menu.