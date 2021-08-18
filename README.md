# RooFit Development - Intuitive Python bindings for RooFit

This report outlines the deliverables completed for the RooFit project, as a part of the Google Summer of Code 2021 program, under the CERN-HSF organization.
Details of the work are described below. Following pythonizations have been implemented along with formatting and pythonization of tutorial files, making them more pythonic and less verbose.

## RooFit functions that take Command Arguments

[PR 1](https://github.com/root-project/root/pull/8413/files) for Pythonization of all functions, such that they take keyword arguments instead by lazily pythonizing classes and using mirror classes. [List](https://github.com/Harshalzzzzzzz/GSoC21_RooFit/blob/main/README.md) of Constructors and functions taht take command arguments.

## RooFit DecayType enum
[PR 2](https://github.com/root-project/root/pull/8467) for enum parameters.

## RooGlobalFunc Functions and Implementing matplotlib Color/Style conventions
[PR 3](https://github.com/root-project/root/pull/8536) for accepting keyword arguments for nested arguments in form of a single value, list, tuple or dictionary and accepting simple color/style conventions from matplotlib.

## Translated RooFit tutorial files
- [PR 4](https://github.com/root-project/root/pull/8584) for this [Issue](https://github.com/root-project/root/issues/8523)
- [PR 6](https://github.com/root-project/root/pull/8705) This PR fixes a problem with the implicit creation of RooFit collections from python collections, where the integer zero is now interpreted as a RooConst and not as a nullptr (Changed signature of constructor to take RooAbsArg by reference). Migrates the RooFit pyROOT tutorials to use python lists instead of RooArgList. Added a translation of the rf408_RDataFrameToRooFit tutorial to pyROOT.

## Dictionary, RooCategory Pythonizations for RooFit
[PR 5](https://github.com/root-project/root/pull/8669) implements a Pythonization for every funciton in RooFit that takes a std::map to also take a Python dictionary.

## RooWorkspace pythonizations
[PR 7](https://github.com/root-project/root/pull/8803) pythonizes the RooWorkspace class by implementing the keyword pythonization for RooWorkspace::import() and implementing `__getitem__` to get dictionary-like syntax to access objects in the workspace. 

## Documentation using Doxygen
[PR 8]() for adding doxygen code at function level for the pythonizations and reformatting/refactoring previously added documentation.
