Download Link: https://assignmentchef.com/product/solved-csci-3155-lab-assignment-3
<br>
The purpose of this lab is to grapple with how dynamic scoping arises and how to formally specify semantics. Concretely, we will extend JAVASCRIPTY with recursive functions and implement two interpreters. The first will be a big-step interpreter that is an extension of Lab 2 but implements dynamic scoping “by accident.” The second will be a small-step interpreter that exposes evaluation order and implements static scoping by substitution.

From your team of 8-10 persons in your lab section, find a new partner for this lab assignment (different from your Lab 1 partner). You will work on this assignment closely with your partner. However, note that <strong>each student needs to submit </strong>and are individually responsible for completing the assignment.

You are welcome to talk about these questions in larger groups. However, we ask that you write up your answers in pairs. Also, be sure to acknowledge those with which you discussed, including your partner and those outside of your pair.

Recall the evaluation guideline from the course syllabus.

<em>Both your ideas and also the clarity with which they are expressed matter—both in your English prose and your code!</em>

<em>We will consider the following criteria in our grading:</em>

<ul>

 <li>How well does your submission answer the questions? <em>For example, a common mistake is to give an example when a question asks for an explanation. An example may be useful in your explanation, but it should not take the place of the explanation.</em></li>

 <li>How clear is your submission? <em>If we cannot understand what you are trying to say, then we cannot give you points for it. Try reading your answer aloud to yourself or a friend; this technique is often a great way to identify holes in your reasoning. For code, not every program that “works” deserves full credit. We must be able to read and understand your intent. Make sure you state any preconditions or invariants for your functions (either in comments, as assertions, or as </em><em>require clauses as appropriate).</em></li>

</ul>

Try to make your code as concise and clear as possible. Challenge yourself to find the most crisp, concise way of expressing the intended computation. This may mean using ways of expression computation currently unfamilar to you.

Finally, make sure that your file compiles and runs on COG. A program that does not compile will <em>not </em>be graded.

1

<strong>Submission Instructions.                  </strong>Upload to the moodle exactly four files named as follows:

<ul>

 <li>Lab3-<em>YourIdentiKey</em>.pdf with your answers to the written questions (scanned, clearly legible handwritten write-ups are acceptable).</li>

 <li>Lab3-<em>YourIdentiKey</em>.scala with your answers to the coding exercises.</li>

 <li>Lab3Spec-<em>YourIdentiKey</em>.scala with any updates to your unit tests.</li>

 <li>Lab3-<em>YourIdentiKey</em>.jsy with a challenging test case for your JAVASCRIPTY</li>

</ul>

Replace <em>YourIdentiKey </em>with your IdentiKey (e.g., I would submit Lab3-bec.pdf and so forth). Don’t use your student identification number. To help with managing the submissions, we ask that you rename your uploaded files in this manner.

Submit your Lab3.scala file to COG for auto-testing. We ask that you submit both to COG and to moodle in case of any issues.

Sign-up for an interview slot for an evaluator. To fairly accommodate everyone, the interview times are strict and <strong>will not be rescheduled</strong>. Missing an interview slot means missing the interview evaluation component of your lab grade. Please take advantage of your interview time to maximize the feedback that you are able receive. Arrive at your interview ready to show your implementation and your written responses. Implementations that do not compile and run will not be evaluated.

<strong>Getting Started. </strong>Clone the code from the Github repository with the following command: git clone -b lab3 https://github.com/bechang/pppl-labs.git lab3

A suggested way to get familiar with Scala is to do some small lessons with Scala Koans <a href="http://www.scalakoans.org/">(</a><a href="http://www.scalakoans.org/">http://www.scalakoans.org/</a><a href="http://www.scalakoans.org/">)</a>. A useful one for Lab 3 is AboutOptions.

<ol>

 <li><strong>Feedback</strong>. Complete the survey on the linked from the moodle after completing this assignment. Any non-empty answer will receive full credit.</li>

 <li><strong>JavaScripty Interpreter: Tag Testing, Recursive Functions, and Dynamic Scoping</strong>.</li>

</ol>

We now have the formal tools to specify exactly how a JAVASCRIPTY program should behave. Unless otherwise specified, we will continue to try to match JavaScript semantics as implemented by Node.js/Google’s V8 JavaScript Engine. Thus, it is still useful to write little test JavaScript programs and run it through Node.js to see how the test should behave. Finding bugs in the JAVASCRIPTY specification with respect to JavaScript is certainly deserving of extra credit.

In this lab, we extend JAVASCRIPTY with recursive functions. This language is very similar to the LETREC language in Section 3.4 of Friedman and Wand.

For this question, we try to implement functions as an extension of Lab 2 in the most straightforward way. What we will discover is that we have made a historical mistake and have ended up with a form of dynamic scoping.

<table width="473">

 <tbody>

  <tr>

   <td width="133">expressionsvalues unary operators binary operators</td>

   <td width="340"><em>e </em>::<em>= x |n |b |str |</em><strong>undefined</strong><em>|uope</em><sub>1 </sub><em>|e</em><sub>1</sub><em>bope</em><sub>2</sub><em>| e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub><em>|</em><strong>const</strong><em>x =e</em><sub>1</sub>; <em>e</em><sub>2 </sub><em>|</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>)<em>| </em><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1 </sub><em>|e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>v </em>::<em>= n |b |</em><strong>undefined</strong><em>|str </em><em>|</em><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1 </sub><em>|</em>typeerror<em>uop </em>::<em>= </em><sup>–</sup><em>|</em>!<em>bop </em>::<em>= </em>,<em>|</em>+<em>|</em>–<em>|</em>*<em>|</em>/<em>|</em>===<em>|</em>!==<em>|</em>&lt;<em>|</em>&lt;=<em>|</em>&gt;<em>|</em>&gt;=<em>|</em>&amp;&amp;<em>|||</em></td>

  </tr>

  <tr>

   <td width="133">variables</td>

   <td width="340"><em>x</em></td>

  </tr>

  <tr>

   <td width="133">numbers (doubles)</td>

   <td width="340"><em>n</em></td>

  </tr>

  <tr>

   <td width="133">booleans strings</td>

   <td width="340"><em>b </em>::<em>= </em><strong>true</strong><em>|</em><strong>false</strong><em>str</em></td>

  </tr>

  <tr>

   <td width="133">function names</td>

   <td width="340"><em>p </em>::<em>= x |ε</em></td>

  </tr>

  <tr>

   <td width="133">value environments</td>

   <td width="340"><em>E </em>::<em>= ·|E</em>[<em>x 7→ v</em>]</td>

  </tr>

 </tbody>

</table>

Figure 1: Abstract Syntax of JAVASCRIPTY

<table width="304">

 <tbody>

  <tr>

   <td width="92">statements expressions</td>

   <td width="213"><em>s </em>::<em>= </em><strong>const</strong>( <em>x =</em>(<em>e |</em>(<em>ee</em>(<em>|</em>(1{(;<em>se</em>(12}<em>||</em>(;<em>e|</em>1<em>s</em>)1 <em>s</em>2<em>e </em>::<em>= ···|</em><strong>const</strong>((<em>x =</em></td>

  </tr>

 </tbody>

</table>

((((

<em>|</em>(<strong>function</strong>((( <em>p</em>(<em>x</em>) <em>e</em>1 <em>|</em><strong>function </strong><em>p</em>(<em>x</em>) { <em>s</em>1 <strong>return </strong><em>e</em>1 }

Figure 2: Concrete Syntax of JAVASCRIPTY

The syntax of JAVASCRIPTY for this lab is given in Figure 1. Note that the grammar specifies the abstract syntax using notation borrowed from the concrete syntax. The new constructs are highlighted. We have function expressions <strong>function </strong><em>p</em>(<em>x</em>) <em>e</em><sub>1 </sub>and function calls <em>e</em><sub>1</sub>(<em>e</em><sub>2</sub>).

In a function expression, the function name <em>p </em>can either be an identifier or empty. When the identifier for the function name is present, it can be used for recursion. For simplicity, all functions are one argument functions. Since functions are first-class values, we can get multi-argument functions via currying.

We have also added a “marker” typeerror to the expression language. This marker is not part of the source language but is used in our definition of the evaluation judgment form. We discuss this in more detail further below.

As before, the concrete syntax accepted by the parser is slightly less flexible than the abstract syntax in order to match the syntactic structure of JavaScript. For function expressions, the body is surrounded by curly braces (i.e., { }) and consists of a statement <em>s</em><sub>1 </sub>for <strong>const </strong>bindings followed by a <strong>return </strong>with an expression <em>e</em><sub>1</sub>.

An abstract syntax tree representation is provided for you in ast.scala. We also provide a

<strong>case class </strong>Function(p: Option[String], x: String, e1: Expr) <strong>extends </strong>Expr

Function(<em>p</em>, <em>x</em>, <em>e</em><sub>1</sub>) <strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1 </sub><strong>case class </strong>Call(e1: Expr, e2: Expr) <strong>extends </strong>Expr Call(<em>e</em><sub>1</sub>, <em>e</em><sub>2</sub>) <em>e</em><sub>1</sub>(<em>e</em><sub>2</sub>)

Figure 3: Representing in Scala the abstract syntax of JAVASCRIPTY. After each <strong>caseclass </strong>or <strong>caseobject</strong>, we show the correspondence between the representation and the concrete syntax. parser and main driver for testing. The correspondence between the concrete syntax and the abstract syntax representation is shown in Figure 3.

A big-step operational semantics of JAVASCRIPTY is given in Figure 4. This figure may be one of the first times that you are reading a formal semantics of a programming language. It may seem daunting at first, but it will be become easier with practice. This lab is such an opportunity to practice.

A formal semantics enables us to describe the semantics of a programming language clearly and concisely. The initial barrier is getting used to the meta-language of judgment forms and inference rules. However, once you cross that barrier, you will see that we are telling you exactly how to implement the interpreter—it will almost feel like cheating!

In Figure 4, we define the judgment form <em>E ` e ⇓ v</em>, which says informally, “In value environment <em>E</em>, expression <em>e </em>evaluates to value <em>v</em>.” This relation has three parameters: <em>E</em>, <em>e</em>, and <em>v</em>. You can think of the other parts of the judgment as just punctuation. This judgment form corresponds directly to the eval function that we are asked to implement (not a coincidence). It similarly has three parts:

<h1><strong>def </strong>eval(env: Env, e: Expr): Expr</h1>

It takes as input a value environment env (<em>E</em>) and an expression e (<em>e</em>) returns a value <em>v</em>.

It is very informative to compare your Scala code from Lab 2 with the inference rules that define <em>E ` e ⇓ v</em>. One thing you should observe is that all of the rules are implemented, except for EVALCALL, EVALCALLREC, and part of EVALEQUALITY. In essence, implementing those rules is your task for this question.

In Lab 2, all expressions could be evaluated to something (because of conversions). With functions, we encounter one of the very few run-time errors in JavaScript: trying to call something that is not a function. In JavaScript and in JAVASCRIPTY, calling a non-function raises a run-time error. In the formal semantics, we model this with evaluating to the “marker” typeerror.

Such a run-time error is known as a dynamic type error. Languages are called <em>dynamically typed </em>when they allow all syntactically valid programs to run and check for type errors during execution.

In our Scala implementation, we will not clutter our Expr type with a typeerror marker. Instead, we will use a Scala exception DynamicTypeError: <strong>case class </strong>DynamicTypeError(e: Expr) <strong>extends </strong>Exception

to signal this case. In other words, when your interpreter discovers a dynamic type error, it should throw this exception using the following Scala code:

<h1><strong>throw </strong>DynamicTypeError(e)</h1>

The argument should be the input expression to eval where the type error was detected. One advantage of using a Scala exception for typeerror is that the marker does not need to be propagated explicitly as in the inference rules in Figure 6. In particular, your interpreter <em>E ` e ⇓ v</em>

EVALVAR                   EVALVAL              E<em>E</em>VAL<em>`e</em>1N<em>⇓ v</em>EG1    <em>n</em><em>0 </em><em>=−</em>toNumber(<em>v</em>1)                       E<em>E</em>VAL<em>`e</em>1N<em>⇓ v</em>OT1     <em>b</em><em>0 </em><em>=¬</em>toBoolean(<em>v</em>1)

<em>E ` x ⇓E</em>(<em>x</em>)                              <em>E ` v ⇓ v                                                             E `−e</em><sub>1 </sub><em>⇓n</em><em>0                                                                                    </em><em>E ` </em>!<em>e</em><sub>1 </sub><em>⇓b</em><em>0</em>

EVALSEQ                                           EVALPLUSNUMBER

<table width="567">

 <tbody>

  <tr>

   <td width="289"><em>E `e</em>1 <em>⇓ v</em>1                  <em>E `e</em>2 <em>⇓ v</em>2                              <em>E `e</em>1 <em>⇓ v</em>1               <em>E `e</em>2 <em>⇓ v</em>2<em>E `e</em>1 , <em>e</em>2 <em>⇓ v</em>2</td>

   <td width="277"><em>n</em><em><sup>0 </sup></em><em>= </em>toNumber(<em>v</em><sub>1</sub>)<em>+</em>toNumber(<em>v</em><sub>2</sub>)                        <em>v</em><sub>1 </sub><em>6=str</em><sub>1            </sub><em>v</em><sub>2 </sub><em>6=str</em><sub>2</sub><em>E `e</em><sub>1 </sub><em>+e</em><sub>2 </sub><em>⇓n</em><em>0</em></td>

  </tr>

  <tr>

   <td width="289">EVALPLUSSTRING1<em>E `e</em><sub>1 </sub><em>⇓str</em><sub>1                </sub><em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2                </sub><em>str</em><em><sup>0 </sup></em><em>=str</em><sub>1 </sub><em>+</em>toString(<em>v</em><sub>2</sub>)<em>E `e</em><sub>1 </sub><em>+e</em><sub>2 </sub><em>⇓str</em><em>0 </em>EVALARITH</td>

   <td width="277">EVALPLUSSTRING2<em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><em>E `e</em><sub>2 </sub><em>⇓str</em><sub>2                 </sub><em>str</em><em><sup>0 </sup></em><em>= </em>toString(<em>v</em><sub>1</sub>)<em>+str</em><sub>2</sub><em>E `e</em><sub>1 </sub><em>+e</em><sub>2 </sub><em>⇓str</em><em>0</em></td>

  </tr>

 </tbody>

</table>

<em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2                </sub><em>n</em><em><sup>0 </sup></em><em>= </em>toNumber(<em>v</em><sub>1</sub>) <em>bop </em>toNumber(<em>v</em><sub>2</sub>)                <em>bop∈ </em>{<em>−</em>,<em>∗</em>,/}

<em>E `e</em><sub>1 </sub><em>bope</em><sub>2 </sub><em>⇓n</em><em>0</em>

<table width="587">

 <tbody>

  <tr>

   <td width="185">EVALINEQUALITYNUMBER1<em>b</em><em>0 </em><em>= </em>toNumber(<em>E `ev</em>11<em>⇓</em>) <em>bopv</em>1 toNumber(<em>E `e</em>2 <em>⇓vv</em>22)<em>E `e</em><sub>1 </sub><em>bope</em><sub>2 </sub><em>⇓b</em><em>0</em></td>

   <td width="124"><em>v</em>1 <em>6=str</em>1<em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}</td>

   <td width="191">EVALINEQUALITYNUMBER2<em>b</em><em>0 </em><em>= </em>toNumber(<em>E `ev</em>11<em>⇓</em>) <em>bopv</em>1 toNumber(<em>E `e</em>2 <em>⇓vv</em>22)<em>E `e</em><sub>1 </sub><em>bope</em><sub>2 </sub><em>⇓b</em><em>0</em></td>

   <td width="88"><em>v</em>2 <em>6=str</em>2<em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}</td>

  </tr>

 </tbody>

</table>

EVALINEQUALITYSTRING

<em>E `e</em><sub>1 </sub><em>⇓str</em><sub>1                 </sub><em>E `e</em><sub>2 </sub><em>⇓str</em><sub>2                </sub><em>b</em><em><sup>0 </sup></em><em>=str</em><sub>1 </sub><em>bopstr</em><sub>2                </sub><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}

<em>E `e</em><sub>1 </sub><em>bope</em><sub>2 </sub><em>⇓b</em><em>0 </em>EVALEQUALITY

<em>E `e</em><sup>1 </sup><em>⇓ v</em><sup>1               </sup><em>E `e</em><sup>2 </sup><em>⇓ v</em><sup>2</sup>

<em>v</em>1 <em>6=</em><strong>function</strong><em>p</em>1(<em>x</em>1) <em>e</em>1                <em>v</em>2 <em>6=</em><strong>function</strong><em>p</em>1(<em>x</em>2) <em>e</em>2                <em>b</em><em><sup>0 </sup></em><em>= </em><sub>(<em>v</em></sub><sub>1 </sub><em>bopv</em><sub>2</sub>)               <em><sub>bop</sub></em><em>∈ </em>{<em>===</em>,! <em>==</em>}

<em>E `e</em><sub>1 </sub><em>bope</em><sub>2 </sub><em>⇓b</em><em>0</em>

EVALANDTRUE                                                                     EVALANDFALSE                                         EVALORTRUE

<em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1                    </sub><strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)               <em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2                       </sub><em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)                       <em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1                </sub><strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)

<em>E `e</em>1 &amp;&amp; <em>e</em>2 <em>⇓ v</em>2 <em>E `e</em>1 &amp;&amp; <em>e</em>2 <em>⇓ v</em>1 <em>E `e</em>1 <em>||e</em>2 <em>⇓ v</em>1 EVALORFALSE EVALPRINT

<em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)               <em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2                                                    </sub><em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1              </sub><em>v</em><sub>1 </sub>printed

<em>E `e</em>1 <em>||e</em>2 <em>⇓ v</em>2                                                                                        <em>E `</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>) <em>⇓</em><strong>undefined</strong>

EVALIFTRUE                                                                             EVALIFFALSE

<em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)               <em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2                                    </sub><em>E `e</em><sub>1 </sub><em>⇓ v</em><sub>1               </sub><strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)               <em>E `e</em><sub>3 </sub><em>⇓ v</em><sub>3</sub>

<em>E `e</em>1 ? <em>e</em>2 : <em>e</em>3 <em>⇓ v</em>2                                                                                                             <em>E `e</em>1 ? <em>e</em>2 : <em>e</em>3 <em>⇓ v</em>3

EVALCONST                                                    EVALCALL

<em>E `e</em>1 <em>⇓ v</em>1               <em>E</em>[<em>x 7→ v</em>1] <em>`e</em>2 <em>⇓ v</em>2                                   <em>E `e</em>1 <em>⇓ v</em>1               <em>v</em><sub>1 </sub><em>=</em><strong>function </strong>(<em>x</em>) <em>e</em><em>0               </em><em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2              </sub><em>E</em>[<em>x →7 v</em><sub>2</sub>] <em>`e</em><em><sup>0 </sup></em><em>⇓ v</em><em>0</em>

<em>E `</em><strong>const</strong><em>x =e</em><sub>1</sub>; <em>e</em><sub>2 </sub><em>⇓ v</em><sub>2                                                                                                                 </sub><em>E `e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>⇓ v</em><em>0</em>

EVALCALLREC

<em>E `e</em>1 <em>⇓ v</em>1                <em>v</em>1 <em>=</em><strong>function</strong><em>x</em><sub>1</sub>(<em>x</em><sub>2</sub>) <em>e</em><em>0                 </em><em>E `e</em><sub>2 </sub><em>⇓ v</em><sub>2             </sub><em>E</em>[<em>x</em><sub>1 </sub><em>→7 v</em><sub>1</sub>][<em>x</em><sub>2 </sub><em>→7    v</em><sub>2</sub>] <em>`e</em><em><sup>0 </sup></em><em>⇓ v</em><em>0</em>

<em>E `e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>⇓ v</em><em>0</em>

Figure 4: Big-step operational semantics of JAVASCRIPTY (with dynamic scoping).

def toNumber(<em>n</em>)              def<em>= n</em>

toNumber(<strong>true</strong>) <em>= </em>1

def toNumber(<strong>false</strong>)        def<em>= </em>0

toNumber(<strong>undefined</strong>) def<em>= </em>NaN toNumber(<em>str</em>)         def<em>= </em>parse <em>str</em>

toNumber(<strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>) <em>= </em>NaN

def toBoolean(<em>n</em>) <em>= </em><strong>false  </strong>if <em>n = </em>0 or <em>n =</em>NaN

def toBoolean(<em>n</em>) <em>= </em><strong>true    </strong>otherwise

def toBoolean(<em>b</em>)               def<em>= b</em>

toBoolean(<strong>undefined</strong>) def<em>= </em><strong>false </strong>toBoolean(<em>str</em>) <em>= </em><strong>false </strong>if <em>str= </em>“”

def toBoolean(<em>str</em>) <em>= </em><strong>true  </strong>otherwise

def toBoolean(<strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>) <em>= </em><strong>true</strong>

def toString(<em>n</em>) <em>= </em>string of <em>n</em>

def toString(<strong>true</strong>) <em>= </em>“true”

def toString(<strong>false</strong>)            def<em>= </em>“false”

toString(<strong>undefined</strong>) def<em>= </em>“undefined” toString(<em>str</em>)  def<em>= str</em>

toString(<strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>) <em>= </em>“function”

Figure 5: Conversion functions. We do not specify explicitly the parsing or string conversion of numbers. The conversion of a function to a string deviates slightly from JavaScript where the source code of the function is returned.

<em>E ` e ⇓ v</em>

EVALTYPEERROREQUALITY1

<em>E `e</em><sub>1</sub><em>⇓v</em><sub>1                         </sub><em>v</em><sub>1</sub><em>=</em><strong>function</strong><em>p</em><sub>1</sub>(<em>x</em><sub>1</sub>) <em>e</em><sub>1                        </sub><em>bop∈ </em>{<em>===</em>,! <em>==</em>}

<em>E `e</em><sub>1 </sub><em>bope</em><sub>2</sub><em>⇓</em>typeerror

<h2>                        EVALTYPEERROREQUALITY2                                                                                                                                 EVALTYPEERRORCALL</h2>

<em>E `e</em><sub>2</sub><em>⇓v</em><sub>2                         </sub><em>v</em><sub>2</sub><em>=</em><strong>function</strong><em>p</em><sub>1</sub>(<em>x</em><sub>1</sub>) <em>e</em><sub>1                        </sub><em>bop∈ </em>{<em>===</em>,! <em>==</em>}                           <em>v</em><sub>1</sub><em>6=</em><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>

<em>E `e</em><sub>1 </sub><em>bope</em><sub>2</sub><em>⇓</em>typeerror                                                           <em>E `v</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>⇓</em>typeerror

<table width="568">

 <tbody>

  <tr>

   <td width="189">EVALPROPAGATEUNARY <em>E `e</em><sub>1</sub><em>⇓</em>typeerror<em>E `uope</em><sub>1</sub><em>⇓</em>typeerror</td>

   <td width="211">EVALPROPAGATEBINARY1 <em>E `e</em><sub>1</sub><em>⇓</em>typeerror<em>E `e</em><sub>1 </sub><em>bope</em><sub>2</sub><em>⇓</em>typeerror</td>

   <td width="168">EVALPROPAGATEBINARY2 <em>E `e</em><sub>2</sub><em>⇓</em>typeerror<em>E `e</em><sub>1 </sub><em>bope</em><sub>2</sub><em>⇓</em>typeerror</td>

  </tr>

  <tr>

   <td width="189">EVALPROPAGATEPRINT <em>E `e</em><sub>1</sub><em>⇓</em>typeerror<em>E `</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>) <em>⇓</em>typeerror</td>

   <td width="211">EVALPROPAGATEIF<em>E `e</em><sub>1</sub><em>⇓</em>typeerror<em>E `e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3</sub><em>⇓</em>typeerror</td>

   <td width="168">EVALPROPAGATECONST <em>E `e</em><sub>1</sub><em>⇓</em>typeerror<em>E `</em><strong>const</strong><em>x =e</em><sub>1</sub>; <em>e</em><sub>2</sub><em>⇓</em>typeerror</td>

  </tr>

 </tbody>

</table>

<h2>                                                EVALPROPAGATECALL1                                                               EVALPROPAGATECALL2</h2>

<em>E `e</em><sub>1</sub><em>⇓</em>typeerror                                                  <em>E `e</em><sub>2</sub><em>⇓</em>typeerror

<em>E `e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>⇓</em>typeerror                                           <em>E `e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>⇓</em>typeerror

Figure 6: Big-step operational semantics of JAVASCRIPTY: Dynamic type error rules.

will implement the EVALTYPEERROR rules explicitly, but the EVALPROPAGATE rules are implemented implicitly with Scala’s exception propagation semantics.

Note in rule EVALEQUALITY, we disallow equality and disequality checks (i.e., <em>=== </em>and ! <em>==</em>) on function values. If either argument to a equality or disequality check is a function value, then we consider this a dynamic type error. This choice is a departure from JavaScript semantics.

(a) First, write some JAVASCRIPTY programs and execute them as JavaScript programs. This step will inform how you will implement your interpreter and will serve as tests for your interpreter.

<strong>Write-up</strong>: Give one test case that behaves differently under dynamic scoping versus static scoping (and does not crash). Explain the test case and how they behave differently in your write-up.

<h1>(b) Then, implement <strong>def </strong>eval(env: Env, e: Expr): Expr</h1>

that evaluates a JAVASCRIPTY expression e in a value environment env to a value according to the evaluation judgment <em>E ` e ⇓ v</em>.

You will again want the following helper functions for converting values to numbers, booleans, and strings:

<h1><strong>def </strong>toNumber(v: Expr): Double <strong>def </strong>toBoolean(v: Expr): Boolean <strong>def </strong>toStr(v: Expr): String</h1>

We suggest the following step-by-step process:

<ol>

 <li>Bring your Lab 2 implementation into Lab 3 and make sure your previous test cases work as expected.</li>

 <li>Extend your implementation with non-recursive functions. On function calls, you need to extend the environment for the formal parameter but not for the function itself. Do not worry yet about dynamic type errors.</li>

 <li>Add support for checking for dynamic type errors.</li>

 <li>Check that your interpreter, unfortunately, implements dynamic scoping instead of static scoping.</li>

 <li>Modify your implementation to support recursive functions.</li>

 <li><strong>JavaScripty Interpreter: Substitution and Evaluation Order</strong>.</li>

</ol>

In this question, we will do two things. First, we will remove environments and instead use a language semantics based on substitution. This change will “fix” the scoping issue, and we will end up with static, lexical scoping.

As an aside, substitution is not the only way to “fix” the scoping issue. Another way is to represent function values as <em>closures</em>, which is a pair of the function with the environment when it is defined. Substitution is a fairly simple way to get lexical scoping, but in practice, it is rarely used because it is not the most efficient implementation.

The second thing that we do is move to implementing a small-step interpreter. A small-step interpreter makes explicit the evaluation order of expressions. These two changes are orthogonal, that is, one could implement a big-step interpreter using substitution or a smallstep interpreter using environments.

<h1>(a) Implement <strong>def </strong>substitute(e: Expr, v: Expr, x: String): Expr</h1>

that substitutes value v for all <em>free </em>occurrences of variable x in expression e. We advise defining substitute by recursion on e. The cases to be careful about are ConstDecl and Function because these are the variable binding constructs. In particular, substitute on expression

a; (<strong>const </strong>a = 4; a)

with value 3 for variable “a” should return

3; (<strong>const </strong>a = 4; a)

not

<h1>3; (<strong>const </strong>a = 4; 3)</h1>

This function is a helper for the step function, but you might want to implement all of the cases of step that do not require substitute first.

<h1>(b) Implement <strong>def </strong>step(e: Expr): Expr</h1>

that performs one-step of evaluation by rewriting the input expression e into a “onestep reduced” expression. This one-step reduction should be implemented according to the judgment form <em>e −→ </em><em><sup>e</sup></em><em>0 </em>defined in Figures 7, 8, and 9. We write <em>e</em>[<em>v</em>/<em>x</em>] for substituting value <em>v </em>for all free occurrences of the variable <em>x </em>in expression <em>e </em>(i.e., a call to substitute).

(c) <strong>Write-up</strong>: Explain whether the evaluation order is deterministic as specified by the judgment form <em>e −→ e</em><em>0</em>.

It is informative to compare the small-step semantics used in this question and the big-step semantics from the previous one. In particular, for all programs where dynamic scoping is not an issue, your interpreters in this question and the previous should behave the same. We have provided the functions evaluate and iterateStep that evaluate “top-level” expressions to a value using your interpreter implementations.

<ol start="4">

 <li><strong>Evaluation Order</strong>.</li>

</ol>

Consider the small-step operational semantics for JAVASCRIPTY shown in Figures 7, 8, and 9. What is the evaluation order for <em>e</em><sub>1 </sub><em>+ e</em><sub>2</sub>? Explain. How do we change the rules obtain the opposite evaluation order?

<ol start="5">

 <li><strong>Short-Circuit Evaluation</strong>. In this question, we will discuss some issues with short-circuit evaluation.</li>

</ol>

<ul>

 <li><strong>Concept</strong>. Give an example that illustrates the usefulness of short-circuit evaluation. Explain your example.</li>

 <li><strong>J</strong><strong>AVASCRIPTY</strong>. Consider the small-step operational semantics for JAVASCRIPTY shown in Figures 7, 8, and 9. Does <em>e</em><sub>1 </sub>&amp;&amp; <em>e</em><sub>2 </sub>short circuit? Explain.</li>

</ul>

<em>e −→ e</em><em>0</em>

DONEG                                                     DONOT

<em>n<sup>0 </sup>=−</em>toNumber(<em>v</em>)                                <em>b<sup>0 </sup>=¬</em>toBoolean(<em>v</em>)                              <sup>D</sup><sup>O</sup><sup>S</sup><sup>EQ</sup>

<em>−v −→n</em><em>0                                                                          </em>!<em>v −→b</em><em>0                                                           </em><em>v</em><sub>1 </sub>, <em>e</em><sub>2 </sub><em>−→e</em><sub>2</sub>

DOPLUSNUMBER                                                                                                                DOPLUSSTRING1

<em>n<sup>0 </sup>= </em>toNumber(<em>v</em><sub>1</sub>)<em>+</em>toNumber(<em>v</em><sub>2</sub>)               <em>v</em><sub>1 </sub><em>6=str</em><sub>1                </sub><em>v</em><sub>2 </sub><em>6=str</em><sub>2                                  </sub><em>str<sup>0 </sup>=str</em><sub>1</sub><em>+</em>toString(<em>v</em><sub>2</sub>)

<em>v</em><sub>1 </sub><em>+ v</em><sub>2 </sub><em>−→n</em><em>0                                                                                                                   </em><em>str</em><sub>1 </sub><em>+ v</em><sub>2 </sub><em>−→str</em><em>0</em>

<table width="618">

 <tbody>

  <tr>

   <td width="480">                   DOPLUSSTRING2                                                          DOARITH<em>str<sup>0 </sup>= </em>toString(<em>v</em><sub>1</sub>)<em>+str</em><sub>2                                                      </sub><em>n<sup>0 </sup>= </em>toNumber(<em>v</em><sub>1</sub>) <em>bop </em>toNumber(<em>v</em><sub>2</sub>)<em>v</em><sub>1 </sub><em>+str</em><sub>2 </sub><em>−→</em><em><sup>str</sup></em><em>0                                                                                                            </em><em>v</em><sub>1</sub><em>bopv</em><sub>2 </sub><em>−→</em><em><sup>n</sup></em><em>0</em>DOINEQUALITYNUMBER1</td>

   <td width="138"><em>bop∈ </em>{<em>−</em>,<em>∗</em>,/}</td>

  </tr>

  <tr>

   <td width="480">                                  <em>b<sup>0 </sup>= </em>toNumber(<em>v</em><sub>1</sub>) <em>bop </em>toNumber(<em>v</em><sub>2</sub>)                  <em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>v</em><sub>1</sub><em>bopv</em><sub>2 </sub><em>−→b</em><em>0</em>DOINEQUALITYNUMBER2</td>

   <td width="138"><em>v</em><sub>1 </sub><em>6=str</em><sub>1</sub></td>

  </tr>

  <tr>

   <td width="480">                                  <em>b<sup>0 </sup>= </em>toNumber(<em>v</em><sub>1</sub>) <em>bop </em>toNumber(<em>v</em><sub>2</sub>)                  <em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>v</em><sub>1</sub><em>bopv</em><sub>2 </sub><em>−→b</em><em>0</em>DOINEQUALITYSTRING <em>b<sup>0 </sup>=str</em><sub>1</sub><em>bopstr</em><sub>2       </sub><em>bop∈ </em>{<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>str</em><sub>1</sub><em>bopstr</em><sub>2 </sub><em>−→b</em><em>0</em>DOEQUALITY</td>

   <td width="138"><em>v</em><sub>2 </sub><em>6=str</em><sub>2</sub></td>

  </tr>

  <tr>

   <td width="480">                <em>v</em><sub>1 </sub><em>6=</em><strong>function</strong><em>p</em><sub>1</sub>(<em>x</em><sub>1</sub>) <em>e</em><sub>1                        </sub><em>v</em><sub>2 </sub><em>6=</em><strong>function</strong><em>p</em><sub>1</sub>(<em>x</em><sub>2</sub>) <em>e</em><sub>2                       </sub><em>b<sup>0 </sup>= </em>(<em>v</em><sub>1</sub><em>bopv</em><sub>2</sub>)<em>v</em><sub>1</sub><em>bopv</em><sub>2 </sub><em>−→b</em><em>0</em></td>

   <td width="138"><em>bop∈ </em>{<em>===</em>,! <em>==</em>}</td>

  </tr>

  <tr>

   <td width="480">DOANDTRUE                                     DOANDFALSE                                 DOORTRUE<strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)                      <strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)                   <strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)<em>v</em><sub>1 </sub>&amp;&amp; <em>e</em><sub>2 </sub><em>−→e</em><sub>2                                           </sub><em>v</em><sub>1 </sub>&amp;&amp; <em>e</em><sub>2 </sub><em>−→ v</em><sub>1                                             </sub><em>v</em><sub>1 </sub><em>||e</em><sub>2 </sub><em>−→ v</em><sub>1</sub></td>

   <td width="138">DOORFALSE <strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)<em>v</em>1 <em>||e</em>2 <em>−→e</em>2</td>

  </tr>

  <tr>

   <td colspan="2" width="618">              DOPRINT                                                                 DOIFTRUE                                            DOIFFALSE<em>v</em><sub>1 </sub>printed                                           <strong>true</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)                        <strong>false</strong><em>= </em>toBoolean(<em>v</em><sub>1</sub>)<strong>console</strong>.<strong>log</strong>(<em>v</em><sub>1</sub>) <em>−→</em><strong>undefined                             </strong><em>v</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub><em>−→e</em><sub>2                                                 </sub><em>v</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub><em>−→e</em><sub>3</sub>DOCALL               DOCALLREC <sup>D</sup><sup>O</sup><sup>C</sup><sup>ONST                       </sup><em>v</em><sub>1 </sub><em>=</em><strong>function </strong>(<em>x</em>) <em>e</em><sub>1  </sub><em>v</em><sub>1 </sub><em>=</em><strong>function</strong><em>x</em><sub>1</sub>(<em>x</em><sub>2</sub>) <em>e</em><sub>1</sub><strong>const</strong><em>x = v</em><sub>1</sub>; <em>e</em><sub>2 </sub><em>−→e</em><sub>2</sub>[<em>v</em><sub>1</sub>/<em>x</em>]                           <em>v</em><sub>1</sub>(<em>v</em><sub>2</sub>) <em>−→e</em><sub>1</sub>[<em>v</em><sub>2</sub>/<em>x</em>]                          <em>v</em><sub>1</sub>(<em>v</em><sub>2</sub>) <em>−→e</em><sub>1</sub>[<em>v</em><sub>1</sub>/<em>x</em><sub>1</sub>][<em>v</em><sub>2</sub>/<em>x</em><sub>2</sub>]</td>

  </tr>

 </tbody>

</table>

Figure 7: Small-step operational semantics of JAVASCRIPTY: DO rules.

<em>e −→ e</em><em>0</em>

<table width="582">

 <tbody>

  <tr>

   <td width="154">SEARCHUNARY <em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0 </em><em>uope</em><sub>1 </sub><em>−→uope</em><sub>1</sub><em>0</em></td>

   <td width="157">SEARCHBINARY1 <em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0 </em><em>e</em><sub>1</sub><em>bope</em><sub>2 </sub><em>−→e</em><sub>1</sub><em>0 </em><em>bope</em><sub>2</sub></td>

   <td width="271">SEARCHBINARYARITH2 <em>e</em><sub>2 </sub><em>−→e</em><sub>2</sub><em>0        </em><em>bop∈ </em>{<em>+</em>,<em>−</em>,<em>∗</em>,/,<em>&lt;</em>,<em>&lt;=</em>,<em>&gt;</em>,<em>&gt;=</em>}<em>v</em><sub>1</sub><em>bope</em><sub>2 </sub><em>−→ v</em><sub>1</sub><em>bope</em><sub>2</sub><em>0</em></td>

  </tr>

  <tr>

   <td width="154">SEARCHEQUALITY2</td>

   <td width="157"> </td>

   <td width="271">SEARCHPRINT</td>

  </tr>

 </tbody>

</table>

<em>e</em><sub>2 </sub><em>−→e</em><sub>2</sub><em>0                   </em><em>v</em><sub>1 </sub><em>6=</em><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1                       </sub><em>bop∈ </em>{<em>===</em>,! <em>==</em>}                                             <em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0</em>

<em>v</em><sub>1</sub><em>bope</em><sub>2 </sub><em>−→ v</em><sub>1</sub><em>bope</em><sub>2</sub><em>0                                                                                  </em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub>) <em>−→</em><strong>console</strong>.<strong>log</strong>(<em>e</em><sub>1</sub><em>0 </em>)

SEARCHIF SEARCHCONST SEARCHCALL1 <em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0 </em><em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0 </em><em>e</em><sub>1 </sub><em>−→e</em><sub>1</sub><em>0</em>

<em>e</em><sub>1 </sub>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub><em>−→e</em><sub>1</sub><em>0 </em>? <em>e</em><sub>2 </sub>: <em>e</em><sub>3                                     </sub><strong>const</strong><em>x =e</em><sub>1</sub>; <em>e</em><sub>2 </sub><em>−→</em><strong>const</strong><em>x =e</em><sub>1</sub><em>0 </em>; <em>e</em><sub>2                                    </sub><em>e</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>−→e</em><sub>1</sub><em>0 </em>(<em>e</em><sub>2</sub>)

SEARCHCALL2 <em>e</em><sub>2 </sub><em>−→e</em><sub>2</sub><em>0</em>

<sup>¡</sup><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub>) <em>−→</em><sup>¡</sup><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub><sup>¢</sup>(<em>e</em><sub>2</sub><em>0 </em>)

Figure 8: Small-step operational semantics of JAVASCRIPTY: SEARCH rules.

<em>e −→ e</em><em>0</em>

TYPEERROREQUALITY1                                                                                             TYPEERROREQUALITY1

<em>bop∈ </em>{<em>===</em>,! <em>==</em>}                                                                     <em>bop∈ </em>{<em>===</em>,! <em>==</em>}

(<strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>) <em>bope</em><sub>2 </sub><em>−→</em>typeerror                             <em>v</em><sub>1</sub><em>bop </em>(<strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>2</sub>) <em>−→</em>typeerror

TYPEERRORCALL <em>v</em><sub>1 </sub><em>6=</em><strong>function</strong><em>p</em>(<em>x</em>) <em>e</em><sub>1</sub>

<em>v</em><sub>1</sub>(<em>e</em><sub>2</sub>) <em>−→</em>typeerror

PROPAGATEUNARY                                      PROPAGATEBINARY                                           PROPAGATEBINARY

<em>uop</em>typeerror <em>−→</em>typeerror                typeerror<em>bope</em><sub>2 </sub><em>−→</em>typeerror                <em>v</em><sub>1</sub><em>bop</em>typeerror <em>−→</em>typeerror

PROPAGATEPRINT                                                                    PROPAGATEIF

<strong>console</strong>.<strong>log</strong>(typeerror) <em>−→</em>typeerror                         typeerror ? <em>e</em><sub>2 </sub>: <em>e</em><sub>3 </sub><em>−→</em>typeerror

PROPAGATECONST                                                              PROPAGATECALL<sub>1                                                         </sub>PROPAGATECALL<sub>2</sub>

<strong>const</strong><em>x =</em>typeerror; <em>e</em><sub>2 </sub><em>−→</em>typeerror             typeerror(<em>e</em><sub>2</sub>) <em>−→</em>typeerror           <em>v</em><sub>1</sub>(typeerror) <em>−→</em>typeerror Figure 9: Small-step operational semantics of JAVASCRIPTY: Dynamic type error rules.