# AI_Assisted_Programming
## MSDS 431 Week 5 Assignment: An Exercise in AI-Assisted Programming

### Assignment Specifications
Return to an earlier assignment in the course: [Go for Statistics](https://github.com/chrisbcaldwell/OLS_Comparison). Recall that this assignment drew on data for the Anscombe Quartet.
* AI-assisted programming. Looking over your repository for Go for Statistics, consider the possibilities of revising your original code with the assistance of a product like GitHub Copilot.
* AI-generated code. Select an LLM-based service or agent such as ChatGPT or Gemini 2.0 Flash. See if you can get your selected LLM agent to generate Go code for Go for Statistics. Provide a step-by-step review of your work. Try various prompts. Build a conversation with the LLM agent. Tailor the conversation to the specific needs of the programming assignment. In your repository for this assignment, include a plain text file of your complete conversation with the LLM agent.  Ensure that your GitHub repository includes dialogs of your conversations with LLM-based services or agents or links to the dialogs. Dialog files should be presented as plain text or portable document format (pdf) files (not MSWord docx files).
* Drawing on the methods above, share as much actual Go code as possible, recognizing that the code may be incomplete or invalid. In the README.md file, refer to the repository file(s) for the code and describe how it was obtained.
* As part of the README.md file, summarize your experiences. What are the possibilities for reducing programming workload?
* As part of the README.md file, also list the training materials that you used, providing the URLs and noting their usefulness in completing the assignment. Your recommendations will be noted and could influence future versions of this assignment.

### AI-Assisted Programming with GitHub Copilot


### AI-Generated Code with ChatGPT
ChatGPT logs can be seen here:
* [Text file](chatgptNewCode.txt)
* [Markdown](chatgptNewCode.md)

In the original assignment [Go for Statistics](https://github.com/chrisbcaldwell/OLS_Comparison) it was suggested to try using the [montanaflynn/stats package](https://github.com/montanaflynn/stats) to perform simple linear regression on [Anscombe's Quartet](https://en.wikipedia.org/wiki/Anscombe%27s_quartet) data.  ChatGPT was asked to perform this task:

> Please write a Go program that performs linear regression on the Anscombe's Quartet data

The resulting program calculated _S<sub>x</sub>_, _S<sub>y</sub>_, _S<sub>xx</sub>_, and _S<sub>xy</sub>_, then calculated slope and intercept using
```
slope := (n*sumXY - sumX*sumY) / (n*sumX2 - sumX*sumX)
intercept := (sumY - slope*sumX) / n
```
This method of course gives correct answers for a single independent variable.  In the case where more than one indpendent variable is used it would be nice to have a program that can handle that, first using the suggested montanaflynn/stats package:

> Rewrite the code to use the stats package at github.com/montanaflynn/stats

ChatGPT gave a mostly valid answer, with a key mistake:
```
slope, intercept, err := stats.LinearRegression(paired)
```

The function `stats.LinearRegression()` returns coordinate pairs that fall on the regression line, using the input data's _x_ values, not a slope and intercept.  This will be revisited shortly.

Next other package options were explored:

> Is there another package besides github.com/montanaflynn/stats that has a better linear regression function?

