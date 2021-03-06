# Housing Price Predication using Linear Regression
> # Machine Learning Assignments of Linear Regression for Housing Price

  In this Project we will make Linear Regression model for simple Housing prizing problem using Python. In this Project we are not using any machine learning liberary ,but insteed we develop function by self. This way we can learn very basic way to implement machine learning. 

  In this project first we are developing machine learning function for linear regression named as LinearRegression(). In Next step we Load training data in function to train and then we check how training being done. 
## Import Useful Liberary :

```
import numpy as np
import matplotlib.pyplot as plt
import math
from housingdata import *
```
#### Some Basic Information for Naming Convention Used :
- X = Features,
- Y = Resposes,
- W = weights - Learning Parameter,
- b = bias - Learning Parameter
- α = Learning Rate,
- m = Training set size
 
 ## Initialize Learning Parameter : 
 
 Here, we initialize learning parameter W and b.
 
 ```
 def initialize_parameters(n_x,method):   
    if method == "Linear":
        W = np.zeros((1,n_x))
        b = 0
    elif method == "Logistic":
        W = np.zeros((1,n_x))
        b = 0
    else :
        print "Error : Define method in initialize parameter"
    return W, b
 ```
 
 ## Feature Normalize : 
 ```
 def feature_normalization(X):
    (row, col) = X.shape
    for f in range(1,row):
        X[f,:] = (X[f,:]- min(X[f,:].T))/(max(X[f,:].T)- min(X[f,:].T))
    assert(X.shape==(row,col)),"Error in size match : feature_normalization"
    return X
 ```
 ## Hyponthsis and Cost Function :
 > h(X) = b + W(1) * X(1) + W(2) * X(2) + W(3) * X(3) ...
 > Cost_Function = ( h(X) - Y )**2
 ```
 def cost_function(X, Y, W, b, method):
    ## where X shape is (input_size, no_examples)
    (n,m) = X.shape
    if method == "Linear":
        hyponthsis_function = np.dot(W,X) + b
        cost = np.square(hyponthsis_function - Y) + lambda * np.sum(W**2)
        error = np.sum(cost /(m),axis=1)
    elif method == "Logistic":
        Z = np.dot(W,X) + b
        hyponthsis_function = 1/(1+exp(-Z))
        cost = - np.dot(Y,np.log(hyponthsis_function).T) - np.dot(1 - Y,np.log(1 - hyponthsis_function).T) + lambda * np.sum(W**2)
        error = np.sum(cost /(m),axis=1)
    else:
        print "Error In Cost Function : No method Found"
        
    return hyponthsis_function, cost, error
 
 ```
 
 ## Gradient Descent :
 
 To implement gradient descent, we will derive following parameters,
 
 - Gradient of cost function J as grad J, I hope you alread know how to get partial derivative of any function, then we can see that gradient of cost is as follow,
 
 > grad J = (1/2) * (h(X) - Y)
 
 - Updating bias b with learning rate α,
 
 >  b := b * (1 - lambda/m) - (α / 2 * m) (**∑** (h(X) - Y) * X(i))
 
 - Updating weight W with learning rate α,
 
 >  W[i] := w[i] * (1 - lambda/m) - (α / 2 * m) (**∑** (h(X) - Y) * X(i))
 
 ```
 def gradient_descent(X, Y, W, b, itertions, learning_rate, method):
    (n,m) = X.shape
    for iteration in range(itertions):
        if method == "Linear":
            dJ = np.dot(W,X) + b - Y 
            db = np.sum(dJ, axis=0)/(2*m)
            dW = np.sum(np.dot(dJ,X.T), axis=0)/(2*m)
        elif method == "Logistic":
            Z = np.dot(W,X) + b
            dJ = Y - Z
            db = np.sum(dJ, axis=0)/m
            dW = np.sum(np.dot(dJ,X.T), axis=0)/m
        else:
            print "Error in gradient descent: No method Found"
        W = W * (1 - lambda/m) - learning_rate * dW
        b = b * (1 - lambda/m) - learning_rate * db
    print dJ.shape
    assert(dJ.shape == (W.shape[0],X.shape[1]))
    assert(db.shape == b.shape)
    assert(dW.shape == W.shape)
    return W,b
 ```
 By implementing this function we make LinearRegression function and training to get optimal Learning Parameters b and W.
 
 ## Visualization Function :  
 
  I have implemented simple visualization function.
  ```
  def visualization(x, y, hf):
    fig, handle = plt.subplots()
    handle.plot(x, y, "yo", x, hf, "--k")
    #handle.plot(x, hf, color='red')
    #handle.scatter(x, y)
    fig.show()
    return None

  ```
  ## Final Linear Regression function :
  
  This is final function that calls all other functions and derive all parameters.
  
  ```
  def linear_regression(X, Y, itertions, learning_rate, method = "Linear"):
    (row, col) = X.shape

    (W, b) = initialize_parameters(row,method)
    X = feature_normalization(X)

    (W,b) = gradient_descent(X, Y, W, b, itertions, learning_rate, method)
    hyponthsis_function, cost, error = cost_function(X, Y, W, b, method)
    
    return hyponthsis_function, error, W, b

  ```
  
 ## Data Reading and Function Call : 
 
 Function that Loads data from .csv file.
 
 ```
 X, Y, Xv, Yv = housingPrice()
 hyponthsis_function, error, W, b = linear_regression(X, Y, itertions=1000, learning_rate=0.5, method="Linear")
 print Y[0,5], hyponthsis_function[0,5]
 ```

## Visualizing Trained model : 

- This is visualization part.

```
x = Xv[1,:].T
x = np.array(x)
x = x.flatten()
y = Yv.T
y = np.array(y)
y = y.flatten()
hf, error, W, b = linear_regression(Xv, Yv, itertions=100, learning_rate=0.5, method="Linear")
hf = np.array(hf)
hf = hf.flatten()
#print X,X.shape,hyponthsis_function.shape
A = visualization(x, y, hf)
```
![Alt text](https://github.com/ChaudhariHarsh/Housing-Price-prediction/blob/master/LinearRe.png)

- As we can see this is very good fit to data.

- This is visualizarion figure with normalization implementation.

![Alt text](https://github.com/ChaudhariHarsh/Housing-Price-prediction/blob/master/LinearReg.png)

- As you can see here that last figure seems to overfit dataset but it does not. We can only visualize two feature but here we are using three and so that this 2D figure cant have that informations.

- So here house price depends on size and its no of badrooms, and we are using third feature as root(size) for noon linear fiting curve, but as we can understand that figure can only show price and size in 2D. So two features are not included in figure.
