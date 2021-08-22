# RooFit Development - Intuitive Python bindings for RooFit

## Abstract

This report outlines the deliverables completed for the RooFit project, as a part of the Google Summer of Code 2021 program, under the CERN-HSF organization.
Part of the ROOT project, RooFit is a statistical modelling library that is widely used in particle physics experiments. The core functionality of RooFit is to enable the modeling of “event data” distributions, where each event is a discrete occurrence in time, and has one or more measured observables associated with it. RooFit maps mathematical data model components to C++ objects, each symbol is represented by a separate object. 

This project aims to simplify complex workflows and enhancement of the python interface, greatly reducing the amount of code that has to be written. A pythonization is a piece of code that injects some new behaviour in a ROOT class, e.g. to add new methods, to make the class iterable from Python or to override arithmetic operators. Pythonizations can be implemented in Python or in C++ (via the Python/C API). In order to make it easier to use ROOT from Python, or to use a more pythonic syntax, PyROOT provides many pythonizations for ROOT classes. In the RooFit pythonization framework, each pythonized class has a pure python mirror class.

Callback is a function or callable object taking two arguments: the Python proxy class to be pythonized and its C++ name. In cppyy, [Python callbacks](https://cppyy.readthedocs.io/en/latest/pythonizations.html), can be written to lazily pythonize a class:
  ```Python
  @pythonization()
  def my_pythonization(klass, name):
      # Parameters:
      # klass: class to pythonize
      # name: string containing the name of the class
      ...adding member function to klass, etc...
  ```


Following pythonizations have been implemented along with formatting and pythonization of tutorial files, making them more pythonic and less verbose.

## RooFit functions that take Command Arguments

- [PR 1](https://github.com/root-project/root/pull/8413/files) : Pythonization of all functions, such that they take keyword arguments instead, by lazily pythonizing classes and using mirror classes. [List](https://github.com/Harshalzzzzzzz/GSoC21_RooFit/blob/main/README.md) of Constructors and functions that take command arguments. Here is an example,

Directly passing RooCmdArgs,
```
mcstudy = ROOT.RooMCStudy(
    model,
    ROOT.RooArgSet(x),
    ROOT.RooFit.Binned(True),
    ROOT.RooFit.Silence(),
    ROOT.RooFit.Extended(),
    ROOT.RooFit.FitOptions(ROOT.RooFit.Save(True), ROOT.RooFit.PrintEvalErrors(0)),
)
```
With keyword arguments,
```
mcstudy = ROOT.RooMCStudy(
    model,
    ROOT.RooArgSet(x),
    Binned=True,
    Silence=True,
    Extended=True,
    FitOptions=dict(Save=True, PrintEvalErrors=0),
)
```
Another Example, Directly passing RooCmdArgs,
```
model.fitTo(data, ROOT.RooFit.Extended(ROOT.kTRUE), ROOT.RooFit.Save())
```
With keyword arguments,
```
model.fitTo(data, Extended=True, Save=True)
```

## RooFit DecayType enum

- [PR 2](https://github.com/root-project/root/pull/8467) : Some RooFit functions and Decay class constructors take enum values as parameters. This PR pythonizes the constructors which take DecayType enums to accept string values. Here is an example :

```
ROOT.RooDecay("decay_gm", "decay", dt, tau, gm, ROOT.RooDecay.DoubleSided)
```
With Pythonizations, Keyword arguments with string values:
```
ROOT.RooDecay("decay_gm", "decay", dt, tau, gm, DecayType="DoubleSided")
```

## RooGlobalFunc Functions and Implementing matplotlib Color/Style conventions

- [PR 3](https://github.com/root-project/root/pull/8536) : Pythonization for accepting keyword arguments for nested arguments in form of a single value, list, tuple or dictionary and accepting simple color/style conventions from matplotlib.

Default bindings for nested command arguments,
```
model.createHistogram("hist", x, ROOT.RooFit.Binning(50), ROOT.RooFit.YVar(y, ROOT.RooFit.Binning(50)))
```
With [RooCmdarg](https://root.cern.ch/doc/master/classRooCmdArg.html) Pythonizations,
```
model.createHistogram("hist", x, Binning=50, YVar=(y, Binning=50))
```
Here is an example for color/style conventions from matplotlib, that have been implemented :
```
pdf.plotOn(frame, ROOT.RooFit.LineColor(ROOT.kRed), ROOT.RooFit.LineStyle(ROOT.kDashed))
```
Passing strings instead of enum values is now supported:
```
pdf.plotOn(frame, LineColor="kRed", LineStyle="kDashed")
```
With the command argument pythonization:
```
pdf.plotOn(frame, LineColor="r", LineStyle="--")
```

## Translated RooFit tutorial files

- [PR 4](https://github.com/root-project/root/pull/8584) : Translates all RooFit tutorials to Python. [Issue](https://github.com/root-project/root/issues/8523)
- [PR 6](https://github.com/root-project/root/pull/8705) : This PR fixes a problem with the implicit creation of RooFit collections from python collections, where the integer zero is now interpreted as a RooConst and not as a nullptr (Changed signature of constructor to take RooAbsArg by reference). Migrates the RooFit pyROOT tutorials to use python lists instead of `RooArgList`. Added a translation of the rf408_RDataFrameToRooFit tutorial to pyROOT.
- Implementing implicit conversion from list/tuple to RooFit collections for RooArgList and RooArgSe.

## Dictionary, RooCategory Pythonizations for RooFit

- [PR 5](https://github.com/root-project/root/pull/8669) : This PR implements a Pythonization for every function in RooFit that takes a std::map to also take a Python dictionary.

Using RooFit functions that take a `std::map` led to code being too verbose, for example:

```
# Alternative constructor form for importing multiple histograms
ROOT.gInterpreter.GenerateDictionary("std::pair<std::string, TH1*>", "map;string;TH1.h")
ROOT.gInterpreter.GenerateDictionary("std::map<std::string, TH1*>", "map;string;TH1.h")
hmap = ROOT.std.map("string, TH1*")()
hmap.keepalive = list()
hmap.insert(hmap.cbegin(), ROOT.std.pair("const std::string,TH1*")("SampleA", hh_1))
hmap.insert(hmap.cbegin(), ROOT.std.pair("const std::string,TH1*")("SampleB", hh_2))
hmap.insert(hmap.cbegin(), ROOT.std.pair("const std::string,TH1*")("SampleC", hh_3))
dh2 = ROOT.RooDataHist("dh", "dh", ROOT.RooArgList(x), c, hmap)
```
With dictionary pythonizations,
```
dh2 = ROOT.RooDataHist("dh", "dh", [x], c, {"SampleA": hh_1, "SampleB": hh_2, "SampleC": hh_3})
```

Another example of the dictionary pythonization is the RooCategory constructor:
```
mixState = ROOT.RooCategory("mixState", "B0/B0bar mixing state", {"mixed": -1, "unmixed": 1})

tagFlav = ROOT.RooCategory("tagFlav", "Flavour of the tagged B0", {"B0": 1, "B0bar": -1})
```

## RooWorkspace pythonizations
The [RooWorkspace](https://root.cern.ch/doc/master/classRooWorkspace.html) has several member functions to access stored objects that [only differ by the type the object is casted to](https://github.com/root-project/root/blob/master/roofit/roofitcore/inc/RooWorkspace.h#L105).
```
  RooAbsPdf* pdf(const char* name) const ;    
  RooAbsReal* function(const char* name) const ;    
  RooRealVar* var(const char* name) const ;    
  RooCategory* cat(const char* name) const ;    
  RooAbsCategory* catfunc(const char* name) const ;    
  RooAbsData* data(const char* name) const ;    
  RooAbsArg* arg(const char* name) const ;    
  RooArgSet argSet(const char* nameList) const ;
```
- [PR 7](https://github.com/root-project/root/pull/8803) : Pythonizes the RooWorkspace class by implementing the keyword pythonization for RooWorkspace::import() and implementing `__getitem__` to get dictionary-like syntax to access objects in the workspace. 

## Documentation using Doxygen
- [PR 8](https://github.com/root-project/root/pull/8860) : Adding doxygen code at function level for the pythonizations and reformatting/refactoring previously added documentation. Documentation of the RooFit pythonizations is done such that it can be picked up by the scripts that generate the doxygen code
