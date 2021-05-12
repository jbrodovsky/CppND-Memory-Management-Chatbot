# CPPND: Memory Management Chatbot

This is the project for the third course in the [Udacity C++ Nanodegree Program](https://www.udacity.com/course/c-plus-plus-nanodegree--nd213): Memory Management. The ChatBot code creates a dialogue where users can ask questions about some aspects of memory management in C++. After the knowledge base of the chatbot has been loaded from a text file, a knowledge graph representation is created in computer memory, where chatbot answers represent the graph nodes and user queries represent the graph edges. After a user query has been sent to the chatbot, the Levenshtein distance is used to identify the most probable answer. The code is fully functional as-is and uses raw pointers to represent the knowledge graph and interconnections between objects throughout the project.

## Dependencies for Running Locally
* cmake >= 3.11
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* wxWidgets >= 3.0
  * Linux: `sudo apt-get install libwxgtk3.0-dev libwxgtk3.0-0v5`. If you are facing unmet dependency issues, refer to the [official page](https://wiki.codelite.org/pmwiki.php/Main/WxWidgets30Binaries#toc2) for installing the unmet dependencies.
  * Mac: There is a [homebrew installation available](https://formulae.brew.sh/formula/wxmac).
  * Installation instructions can be found [here](https://wiki.wxwidgets.org/Install). Some version numbers may need to be changed in instructions to install v3.0 or greater.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./membot`.

## Project Task Details

The initial warmup bug that caused the program to crash was fixed by removing the call to `delete _chatbot` present in the `graphnode.cpp` destructor. It is more appropriate to have that be included in the `chatlogic.cpp` destructor.

Aside from the bug mentioned above, there are five additional major student tasks in the Memory Management chatbot project, which are:

### Task 1 : Exclusive Ownership 1
In file `chatgui.h` / `chatgui.cpp`, I made `_chatLogic` an exclusive resource to class `ChatbotPanelDialog` by making use of a unique pointer. I also changed the constructor and destructor of ChatBotPanelDialog to incorporate the change to a smart pointer.

### Task 2 : The Rule Of Five
In file `chatbot.h` / `chatbot.cpp`, I made changes to the class `ChatBot` such that it complies with the Rule of Five. I added a copy, copy assignment, move, and move assignment constructors.

### Task 3 : Exclusive Ownership 2
In file `chatlogic.h` / `chatlogic.cpp`, I adapted the vector `_nodes` to be a vector of unique pointers to `GraphNodes` to ensure the vector elements are exclusively owned by the class `ChatLogic`. This required modifications to several functions. The destructor was modified to remove code destroying each of the nodes as the use of smart pointers enables auto-destruction when out of scope. Additionally, `LoadAnswerGraphFromFile` was converted to make use of the new smart pointer semantics.

### Task 4 : Moving Smart Pointers

In files `chatlogic.h` / `chatlogic.cpp` and `graphnode.h` / `graphnode.cpp` I changed the ownership of all instances of `GraphEdge` by using a unique pointer such that each instance of `GraphNode` exclusively owns the outgoing `GraphEdges` (the 'child' edges) and holds non-owning references to incoming `GraphEdges` (the 'parent' edges). 

### Task 5 : Moving the ChatBot

In file `chatlogic.cpp`, I created a local `ChatBot` instance on the stack at the bottom of function `LoadAnswerGraphFromFile`. Then, I used move semantics to pass the `ChatBot` instance into the root node, and made sure that `ChatLogic` had no ownership relation to the `ChatBot` by removing the instances created in the constructor. Thus `ChatLogic` is no longer responsible for memory allocation and deallocation, but `ChatLogic` is still able to communicate with the GUI through the member `_chatBot` of `ChatLogic`. Changes were made in in files `chatlogic.h` / `chatlogic.cpp` and `graphnode.h` / `graphnode.cpp` to encorporate this. When the program was executed, a segmentation fault came up and lead to further debugging which revealed an improperly written move assignment constructor. With that fixed the program ran properly and displays the messages on which part of the Rule of Five components of `ChatBot` is called should be printed to the console. When sending a query to the `ChatBot`, the output should look like the following: 

```
ChatBot Constructor
ChatBot Move Constructor
ChatBot Move Assignment Operator
ChatBot Destructor
ChatBot Destructor 
```
