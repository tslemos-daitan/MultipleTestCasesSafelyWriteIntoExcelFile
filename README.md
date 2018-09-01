[Katalon Tips] Multiple Test Cases write data into a single Excel file
======================================================================

This is a [Katalon Studio](https://www.katalon.com/) project for demonstration purpose.
You can clone this out to your PC and run it with your local Katalon Studio.

This project was developed using Katalon Studio v5.5

This project was developed in response to the post in the
Katalon Forum:

- [Writing Data in Excel](https://forum.katalon.com/discussion/9316/writing-data-in-excel)

The questioner of the post wanted Katalon Test Cases/Test Suites to write data into an Excel file.

I think that the theme is not trivial. It requires appropriate design and some amount of programming efforts. I tried to create a demo project here.

## Problem to solve

I wanted to find out a solution to the following points:

1. I want my Katalon Studio project to generate a single Excel file where my Test Cases store anonymous data.
2. I want to name the Excel file in such format. If I start a Test Suite named `TS_a` then it creates a file named `TS_a` followed by the timestamp when it was started. For example,  it would be `TS_a.20180901_091020.xls`. If I select a Test Case named `TC1` then it will create a file named `TC1.xls` without timestamp.
3. I want 2 or more Test Cases in a Test Suite share a file and update it mutually. Test Case `TC2` should not carelessly erase data written in the file by preceding Test Case `TC1`.
4. I want to create sheet per each test case. If I have a Test Suite which contains 2 Test Cases `TC1` and `TC2`, then running the Test Suite should result a Excel file containing 2 sheets: `TC1` sheet and `TC2` sheet.

## How to run the demo

You can run the demo in 3 ways:
1. You can select a single Test Case and run it. Running a Test Case `Test Cases/TC1` will result a file `Excel files/TC1.xls`.
2. You can select a single Test Suite `TS_a` to run consisting Test Cases sequentially. Running a Test Suite `Test Suites/TS_a` will result a file `Excel files/TS_a.yyyyMMdd_hhmmss.xls`.
3. You can run a Test Suite Collection `Test Suites/Execute`. It will result 2 files: `TS_a.yyyyMMdd_hhmmss.xls` and `TS_b.yyyyMMdd_hhmmss.xls`.

## Design note

How can multiple Test Cases in a Katalon project share a single Excel file? I do not like the file written by a preceding Test Case `TC1` cleared by a Test Case `TC2` which follows; how can I implement this mutual file control?

I found that combination of the following points solve the problem.

1. Each Test Case on start-up should look for the target Excel file. If it find the file, it should open the file and update it. If it does not find the file, it shoud create new file.
2. Use GlobalVariable.WORKBOOK. In the GlobalVariable I would put the object instance of HSSFWorkbook class (representation of Excel book by Apache POI). The Test Cases of a Test Suite can share the HSSFWorkbook object in the GlobalVariable during a Test Suite run. Passing the HSSFWorkbook via GlobalVariable enables a Test Case `TC3` updates the sheet created by preceding `TC1` and `TC2`.
3. Each Test Case should serialize the HSSFWorkbook object into file when the Test Case goes shotdown.
