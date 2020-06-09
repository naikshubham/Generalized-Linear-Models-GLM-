# Generalized-Linear-Models-GLM
Extend regression toolbox with the logistic and Poisson models, by learning how to fit, understand, assess model performance and finally use the model to make predictions on new data.

### Going beyond linear regression
- GLMs provide a versatile framework for statistical modeling of data and are often used to solve pratical probelms.GLMs are generalization of linear models.

<p align="center">
  <img src="images/linear_model.JPG" width="350" title="GLM">
</p>

- Suppose we would like to predict salary given years of experience. In regression terms we would write it as salary is predicted by exprience where tilde means "predicted by". (salary ~ experience). `salary = beta0 + beta1 * experience + epsilon`

#### LINEAR MODEL - ols()
- To fit linear models in python we use `statsmodels ols()` function.

```python
# Linear Model
from statsmodels.formula.api import ols

model = ols(formula = 'y ~ X', data=my_data).fit()
```

#### GENERALIZED LINEAR MODEL - glm()

```python
import statsmodels.api as sm
from statsmodels.formula.api import glm

model = glm(formula = 'y ~ X', data=my_data, family=sm.families.____).fit()

# family denotes the probability distribution of the response variable
```

#### Assumptions of linear models
- Using the ols function we obtain the linear fit.The regression function tells us how much the response variable y changes, on average, for a unit increase in x. The model assumptions are 
1. linearity in the parameters,
2. the errors are independent and normally distributed
3. and the variance around the regression line is constant for all values of x.
- But what if the response is not continuous but binary or count ? Or the variance of y is not constant -> depends on the mean. Can we still fit a linear model ?

<p align="center">
  <img src="images/response.JPG" width="350" title="Response">
</p>

#### Linear model and binary response

<p align="center">
  <img src="images/linear_glm.JPG" width="350" title="Response">
</p>

1. First we fit a linear model using ols function. Taking the weight at 5.2 and reading of the probability value, we see the fit is structually wrong since we get a value greater than 1, which is not possible by our data.
2. To correct for this we fit a GLM, shown in blue, with the binomial family corresponding to Binomial or logistic regression. Visually there is a significant difference in the fitted models.
3. Now for a value of 5.2, we obtain a probability of 0.99, which is inline with binary data, since it is bounded by 0 and 1.

#### From probabilities to classes

<p align="center">
  <img src="images/probabilities.JPG" width="350" title="Probabilities">
</p>

- To obtain the binary class from computed probabilities, we split the probabilities at say 0.5 which for the weight of 5.2 gives the Yes class. Similarly for weight 1.5 we obtain no class.
- The Binomial GLM provides the means to compute estimated probabilities in the range of the response variable for binary data, unlike linear models which give out of range estimations.

### Build a GLM
- Linear models are not suitable to accommodate different than continuos response data and provide rather strange results. We can overcome such problems with GLMs, which `provide a unified framework for modeling data originating from the exponential family of densities which include Gaussian, Binomial and Poissan among others.`

#### Components of the GLM
- There are 3 components of GLM,

<img src="images/components_1.JPG" width="350" title="Components">
<img src="images/components_2.JPG" width="350" title="Components">

1. the `random component` which defines the response variable y and its probability distribution. One assumption here is that the observations y_1 to y_n are independent.
2. the second component is the `systematic component` which defines which explanatory variables to include in the model. We can include p different explanatory variables. It allows for interaction effects, where one variable depends on another and vice versa or curvilinear effects.
3. the 3rd component is the link function, which connects the random and systematic component.It is the function of the expected value of the response variable which enables linearity in the parameters. By its construction it allows the mean of the response variable to be nonlinearly related to the explanatory variables. **It is the link function which generalized the linear model.**

- The choice of the link function is separate from the choice of random component. Let's review the most common data types and how they are represented in the GLM framework.

#### Continuos -> Linear Regression

<img src="images/continuous.JPG" width="350" title="continuous">

- One data type we are familiar with is continuous and approximately normally distributed with the real line as the domain.
- Examples : House prices, salary, person's height etc.
- When fitting a GLM we would use Gaussian for the distribution family where the link function is the identity. The identity link function is of the simplest form where it equals mu or the mean of the response. 
- As such it specifies the linear regression model where y is assumed continuous and for which we can assume a normal distribution for the response. Therefore, the linear regression is the special case of the GLM.

#### Binary -> Logistic Regression

<img src="images/binary.JPG" width="350" title="continuous">

- another data type we encounter quite often is binary data, i.e data with two possible classes, which we usually denote with 0/1 where 1 is true and 0 is false.
- To fit a GLM we should use Binomial distribution where the default link function is the logit. Models of this form are called logistic regression.

#### Count -> Poisson Regression

<img src="images/count.JPG" width="350" title="count">

- Count data are positive and some examples include the number of hurricanes, number of bike crossing on a bridge. etc.
- To fit a GLM we should use Poisson for the distribution where the default link function is logarithm. Models of this form is called Poisson regression.

#### Link functions and their usage

<img src="images/link.JPG" width="350" title="Link Functions">

#### Benefits of GLM
- A unified framework for many different data distributions, **Exponential family of distributions**
- **Link function** : The link function transforms the expected value of y and not y itself, and it enables linear combinations, which further provide benefits that many techniques from linear models apply to GLMs as well.

### How to fit a GLM in Python

#### statsmodels
- The starting point is the statsmodels library, which is used for statistical and econometric analysis.

```python
import statsmodels.api as sm
import statsmodels.formula.api as smf
from statsmodels.formula.api import glm  # import glm() directly
```

#### Process of model fit
1. To fit a model we first need to describe the model using the model class glm. -> `glm()`
2. Then the method fit is used to fit the model. -> `.fit()`
3. Very detailed results of the model fit can be analyzed via the summary method. -> `.summary()`
4. Finally, we can compute predictions using the predict method. -> `.predict()`

#### Describing the model
- There are two ways to describe the model, **using formulas or arrays**.

##### Formula based

```python
from statsmodels.formula.api import glm

model = glm(formula, data, family)
```

- The statsmodels uses the pasty package to convert formulas and data to matrices which are then used in model fitting.
- The main arguments are formula, data and family.

##### Array based

```python
import statsmodels.api as sm

X = sm.add_constant(X)
model = sm.glm(y, X, family)
```

- Note that in array-based method the intercept is not included by default. We can add it using the `add_constant` function.

#### Formula Argument

<img src="images/formula.JPG" width="350" title="formula">

- The formula is at the heart of the modeling function, where the response or output is modeled as a function of the explanatory variables or the inputs.

<img src="images/formula_1.JPG" width="350" title="formula">

- **Each explanatory variable is specified and separated with a plus sign.** Note that the formula needs to be enclosed in quotation marks. There are different ways we can represent explanatory variables in the model.
- Categorical variables are enclosed with **Capital C**, removing the intercept is done with minus one, the interaction terms are written in two ways depending on the need, where the semicolon applies to only the interaction term, whereas the multiplication symbol will also, in addition to the interaction term, add individual variables.
- Lastly, we can also add transformations of the variables directly in the formula.

#### Family Argument

`family = sm.families._ _ _ _()`

<img src="images/family.JPG" width="350" title="family">

- Family distributions are in the families namespace. The default link function is denoted in parenthesis, but we can choose other link functions available for each distribution.
- However, if we choose to use a non-default link function, we would have to specify it directly.

#### Summarizing the model

- `model_GLM.summary()`

- To view the results of the model fit we use the summary method, which provides the main information on model fit, such as the model description, model statistics, such as log-likelihood and deviance, and estimated model parameters with their corresponding statistics.
- The estimated parameters are given by coef with their standard error, z scores, p-values and 95% confidence intervals.

#### Regression coefficients
- `.params` prints regression coefficients -> `model_GLM.params`
- To only view the regression coefficients we can use params given model fit.
- Similarly, the confidence intervals for the parameter estimates can be obtained by calling `conf_int` -> `model_GLM.conf_int()`
- `.conf_int(alpha=0.05, cols=None)`
- The default is 95% which we can change using the alpha argument.With cols argument, we can specify which confidence intervals to return.

#### Predictions
- While doing predictive analytics, the final goal is to compute and assess predictions given the fitted model and test data.
- The first step is to specify the test data which should contain all the variables we have included in the final model.
- `.predict(test_data)` computes predictions
- `model_GLM.predict(test_data)`



























































































































