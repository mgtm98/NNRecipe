# NNRecipe
A Deep-Learning framework -for learning purposes- inspired by keras

## Framework Design
NNRecipe is divided into 3 main modules: 
  - **NN**: Neural Network, Implements most of the Neural Network API like: layers, activation functions, etc.
  - **Opt**: Optimizers, Implements diffrent current of optimizers for NN module like: GD, GDAdam, etc.
  - **Loader**: Data Loader mechanism to save/load the neural network, load data sets, etc.
  - **Visual**: Visualization module to implement some visulatizations for the Neural Network training.

![Class Diagram](design.png?raw=true "Title")
![Opt Diagram](optimizer_class_diagram.jpg?raw=true "Title")
![LossFunc Diagram](LossFunction.png?raw=true "Title")
![Layer Class Diagram](Layer.png?raw=true "Title")

## How to install
pip install -i https://test.pypi.org/simple/ nn-recipe
  
## How to use
1. Create An MNIST Model from a dense layer then save the model to the Desktop
``` python
from nn_recipe.NN.ActivationFunctions import *
from nn_recipe.NN.LossFunctions import *
from nn_recipe.NN.Layers import *
from nn_recipe.Opt import *
from nn_recipe.NN import Network
import numpy as np
from nn_recipe.utility import OneHotEncoder
from nn_recipe.DataLoader import MNISTDataLoader

# Mnist Dense layer Model construction
net = Network(
    layers=[
        Linear(in_dim=784, out_dim=25, activation=ReLU()),
        Linear(in_dim=25, out_dim=10, activation=Identity())
    ],
    optimizer=GDAdaGrad(learning_rate=0.01),
    loss_function=MClassLogisticLoss(sum=True, axis=0),
)

# Loading Data fro mnist Data set
mnist = MNISTDataLoader(rootPath="C:\\Users\\mgtmP\\Desktop\\NNRecipe\\mnist", download=False)
mnist.load()
X = mnist.get_train_data().reshape((-1, 28 * 28))
Y = mnist.get_train_labels().reshape((-1, 1))
X_validate = mnist.get_validation_data().reshape((-1, 28 * 28))
Y_validate = mnist.get_validation_labels().reshape((-1, 1))
X_test = mnist.get_test_data().reshape((-1, 28 * 28))
Y_test = mnist.get_test_labels().reshape((-1, 1))
X = X / 255
X_validate = X_validate / 255
X_test = X_test / 255

# Creating Label encoder
encoder = OneHotEncoder(
    types=[0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0],
    active_state=1,
    inactive_state=0
)
y_encoded = encoder.encode(Y)


# verify callback to check for validation accuracy
def verify():
    f = net.evaluate(X_validate)
    y_hat = encoder.decode(f)
    y_hat = np.array(y_hat).reshape((-1, 1))
    return np.count_nonzero(y_hat - Y_validate)


# train the model
net.train(X, y_encoded, notify_func=None, batch_size=100, max_itr=10, verify_func=verify)

# check for model accuracy using the test dataset
out = net.evaluate(X_test)
yhat = encoder.decode(out)
yhat = np.array(yhat).reshape((-1, 1))
print("Total Accuracy is :", 1 - np.count_nonzero(yhat - Y_test) / Y_test.shape[0])

# Save The model for later use
net.save("C:\\Users\\mgtmP\\Desktop\\mnist_network_model_3_38.net")
```

2. Load an existing MNIST Model and use it
``` python
from nn_recipe.NN import Network
import numpy as np
from nn_recipe.utility import OneHotEncoder
from nn_recipe.DataLoader import MNISTDataLoader


# Loading Data fro mnist Data set
mnist = MNISTDataLoader(rootPath="C:\\Users\\mgtmP\\Desktop\\NNRecipe\\mnist", download=False)
mnist.load()
X_test = mnist.get_test_data().reshape((-1, 28 * 28))
Y_test = mnist.get_test_labels().reshape((-1, 1))
X_test = X_test / 255

# Creating Label encoder
encoder = OneHotEncoder(
    types=[0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0],
    active_state=1,
    inactive_state=0
)

# Mnist Dense layer Model construction
net = Network.load("C:\\Users\\mgtmP\\Desktop\\mnist_network_model_3_38.net")


# check for model accuracy using the test dataset
out = net.evaluate(X_test)
yhat = encoder.decode(out)
yhat = np.array(yhat).reshape((-1, 1))
print("Total Accuracy is :", 1 - np.count_nonzero(yhat - Y_test) / Y_test.shape[0])


```
