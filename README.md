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
This method of course gives correct answers, but it would be nice to avoid writing a linear regression formula, first using the suggested montanaflynn/stats package:

> Rewrite the code to use the stats package at github.com/montanaflynn/stats

ChatGPT gave a mostly valid answer, with a key mistake:
```
slope, intercept, err := stats.LinearRegression(paired)
```

The function `stats.LinearRegression()` returns coordinate pairs that fall on the regression line, using the input data's _x_ values, not a slope and intercept.  This will be revisited shortly.

Next other package options were explored:

> Is there another package besides github.com/montanaflynn/stats that has a better linear regression function?

Three packages were suggested:
1. `gonum/stat` from `gonum.org/v1/gonum` - a valid response
2. `gostat` from `github.com/matrixorigin/simdcsv` - this package does not exist; the package address is for a CSV parser
3. `golearn` from `github.com/sjwhitworth/golearn` - a partially valid response; linear regression functions are in `golearn/linear_models`

Code was given only for the `gonum/stat` option (I wish it had tried #2!).  Like before, there were instances of functions using the wrong number of inputs and types.

Because a working program had already been implemented using `montanaflynn/stats`, repairing the code was attempted in ChatGPT:

> in montanaflynn/stats, the function stats.LinearRegression should return a series of coordinate pairs and error, not slope, intercept, and error

In this response the earlier error was corrected.  Slope and intercept were calculated using the first two coordinate pairs returned:

```
// Calculate slope and intercept
		if len(predictedCoords) > 0 {
			// Take first two coordinates for calculating slope and intercept
			slope := (predictedCoords[1].Y - predictedCoords[0].Y) / (predictedCoords[1].X - predictedCoords[0].X)
			intercept := predictedCoords[0].Y - slope*predictedCoords[0].X

			fmt.Printf("  Slope:     %.4f\n", slope)
			fmt.Printf("  Intercept: %.4f\n", intercept)
			fmt.Printf("  Equation:  y = %.4fx + %.4f\n", slope, intercept)
```

In the fourth data set of Anscombe's Quartet, this presents a problem: all but one coordinate pair uses the same _x_ value.  This would, using the response's method, return an error by dividing by zero when calculating slope.  This was addressed next:

> what if the first two data points have the same x value?

ChapGPT responded by checking if the first two _x_ values are equal:

```
// Check for duplicate X values
	if coords[0].X == coords[1].X {
		return 0, 0, fmt.Errorf("duplicate X values detected: cannot compute slope")
	}
```

This of course is insufficient; we need to only abandon the slope calculation if _all_ _x_ values are equal:

> if the first two x values are equal, could we check to see if there are any two x values that are different and use those coordinates to get a slope?

After this prompt, ChatGPT gave a response that logically resembled the program I had written already using `montanaflynn/stats`.  There was one inefficient `for` loop written though, iterating through the _x_ values in the coordinate pairs returned by the `stats.LinearRegression` function:

```
// Find first pair with different X values
	for i := 0; i < len(coords); i++ {
		for j := i + 1; j < len(coords); j++ {
			if coords[i].X != coords[j].X {
				slope = (coords[j].Y - coords[i].Y) / (coords[j].X - coords[i].X)
				intercept = coords[i].Y - slope*coords[i].X
				return slope, intercept, nil
			}
		}
	}
```

It is unneccessary to loop through all _x_ values, searching all remaining _x_ values for equality; if all _x_ values match `coords[0].X` then all are equal, the line is vertical, and there is no need to search further.  I stopped my ChatGPT session here.

### AI Code Revision With ChatGPT

I asked ChatGPT to look at my code for the assignment as it was submitted:

> I've already written a program to perform linear regression on Anscombe's Quartet.  The Go code is at [https://github.com/chrisbcaldwell/OLS\_Comparison/blob/main/Go/anscombe/anscombe.go](https://github.com/chrisbcaldwell/OLS_Comparison/blob/main/Go/anscombe/anscombe.go).  Could you suggest improvements?

ChatGPT offered a list of suggestions without code implementation details, instead offereing that it could go into more detail if desired:

✅ 1. **Error Handling for Duplicate X Values**

In datasets like Anscombe's Quartet, duplicate `X` values can lead to division by zero when calculating the slope. Implementing a check to identify and handle such cases is crucial. Consider iterating through the dataset to find any two points with different `X` values before computing the slope.

✅ 2. **Modularize Code for Reusability**

Breaking down your code into smaller, reusable functions can improve readability and maintainability. For instance:

* **Data Loading**: Separate function to load and parse the dataset.
* **Linear Regression**: Encapsulate the regression logic in its own function.
* **Visualization**: Create a function dedicated to plotting the results.

✅ 3. **Enhance Output with Statistical Metrics**

Providing additional statistical metrics can offer deeper insights into the regression model's performance:

* **Mean Squared Error (MSE)**: Indicates the average squared difference between observed and predicted values.
* **R-squared (R²)**: Measures the proportion of variance in the dependent variable that's predictable from the independent variable.

These metrics can help assess the fit quality of your regression model.([skillcamper.com][1])

✅ 4. **Implement Cross-Validation**

To evaluate the generalizability of your model, consider implementing k-fold cross-validation. This technique splits the dataset into k subsets, training the model k times, each time using a different subset as the test set and the remaining as the training set. It provides a more reliable estimate of model performance.([DataScienceBase][2])

✅ 5. **Optimize Performance for Larger Datasets**

While Anscombe's Quartet is small, if you plan to scale your implementation to larger datasets, consider:

* **Efficient Data Structures**: Use slices or arrays for storing data points to minimize overhead.
* **Parallel Processing**: For computationally intensive tasks, leverage Go's goroutines to parallelize operations.

✅ 6. **Improve Code Documentation**

Adding comments and documentation to your code can make it more understandable for others and for future you:

* **Function Comments**: Describe the purpose and parameters of each function.
* **Inline Comments**: Explain complex or non-intuitive code sections.([WIRED][3])

✅ 7. **Consider Using a Linear Algebra Library**

For more complex regression models or matrix operations, you might explore Go libraries like `gonum` or `golearn`. These libraries offer optimized functions for linear algebra and machine learning tasks.

---

My thoughts on these suggestions are as follows:

1. **Error handling for slope and intercept calculation:** My code already does this.  Excellent suggestion, though it is a disappointment to see this rate #1.
2. **Modular code:** Because the project was a comaprison of existing libraries, this was not a priority but in general this is a good software development goal.
3. **Statistical metrics:** Agreed, my linear regression function only calculates slope and intercept.  The lack of a robust, all-inclusive regression function is a limitation of `montanaflynn/stats`.
4. **k-fold cross validation:** This is well beyond the scope of the project, but I do agree that a publication-worthy comparison of regression capabilities would test [k-fold validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) as well.
5. **Optimization:** Data structure efficiency is a tradeoff while using `montanaflynn/stats`; the `Series` and `Coordinate` types used in the package break the data into coordinate pairs.  Linear regression is a straight-forward matrix calculation and something more resembling a data frame in R or Python would perform much better.
6. **Documentation:** I think my code sufficiently descsribes its goals, given that the user should already be experienced in statistical methods.  This is easily actionable advice though and I will check to see what I can improve.
7. **Linear algebra library:** This is the best suggestion offered, and seeing it as the very last one is not encouraging.
