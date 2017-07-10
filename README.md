# Python Scrapy for Leetcode

## Purpose

To practice scrapy that I recently learned. Cuz leetcode.com is the website that I play with all the time. I wanna build a project about leetcode. Features it surppose to have:

	* Get test case of all problems from leetcode.com, able to verify user's soluiton on local env.

	* When new problem released, it could automatically get its test case through simple commands.


## APPROACH

Generally on leetcode, the backend will create an instance of solution class，then call its functions with test case. There are several approach to get the test case.

**Approch I:**

Submit wrong answer, so the server will return the case and correct answers. We can do this for each case but it is almost impossible.
	1,Some problem has really big amount of test case. it is impossible to submit on one probelm thunsand of times.
	2,Output could be huge something, it will exceed the limitation of sumbition if we submit back the ouput each time.
	3,Cases of some problem even cannot be compared such as design problem.

**Approch II(using):**

Using static container and counter, collect cases into the container when the function be called each tome. When I get to the last case, print all the cases that I collected, and return a wrong answer to make it stop. The server will return the std_out and the wrong answer. The content of the std_out is the cases I just collected. This is the solution I am using, but it also has some limitation.

In order to collect cases, I need the correct solution for all probelm at the first place. How could I get all of these solutions, after hours of "google", I decide to make this automatic. It means I wanna to get the solution from the discuss page. The processsis like:

Crawl all problems -> Go to the discuss page -> Extract the soluiton code -> Varify the correctivty of the code -> Insert my own "case collecting code" into the code -> Submit the code -> Get test cases.


## IMPLEMENTATION

The program is mush complicate than I thought.

### solutions_spider.py
A crawler running under the Scrapy framework. It starts from the leetcode home page, to get the meta data of all problems. Each meta data contains id, name, url. In the url, it could get the description of problem, the default code of all languages.

### questionmeta.py 
A class to maintain the meta data. It is used many where so I encapsulate it.

### questioncontent.py
A class to maintain the description and default code. The most annoying thing is encoding and decoding

### questionsolutionservice.py
A class to maintain the solution code. Analyzing the code that extracted from discuss page to verify it is sytanx correctivity. Providing apis for other modules.

### user.py
A user class to manage the user's behavior. An instance of user class will simulate the login, mantain a session, also submit, get the result. I get the submission api from the http package. The submission process is like: Post the submission request through the problem's url -> if there is no cookie error, the server will return the associated submission id. I can use this id to check the result repeatly. The return is in format of status_code, such as wrong answer, time limit exeeded, runtime error, accrpted, etc. 

### usermanager.py
To encapsulate the behavior of user instances. Cuz I can not using one account submit frequently, I am using ten accounts to submit code and to collection test cases in multi-thread. The code I submited is got from another module, encapsulation make everything easier.

### verifiedsolutionservice.py
To maintain the verified solustion code as well as the "collection case" version of soluiton code. When i went here, the whole project has got annoying, interface's name got unorganized.

### codegen.py
To generate the collecting case code into the solution code(Only java solution). It called directly by VerifiedSolutionService. Its core code is like below: 

```
func_body = 
func_body.format(self.__body(
                        self.__printer(
                            self.__printer_body(
                                self.__formatter(
                                    self.__sub_printer(
                                        self.__recur_printer
                                                        ))))))
```

### syntaxparser.py
A syntax analyzor, the analyze the default code to get the tyoe of input and output. I create a TypeTree to manage type, Cuz there is not too much types, TypeTree is easy to implement.

## ISSUES
* For now, I could got test case of 90% of problem. The biggest challeng remain is Design problem, Cuz there is no standard way / order to call function of the soluiton class. It is impossible to do it in my old way cuz I dont know when to return a wrong answer to terminate the test.


 
