# PlanOut

PlanOut is a language for online field experimentation. It lets you implement both simple and complex experiments with only a few lines of code.

This release currently includes

  * The reference implementation of the PlanOut interpreter. This reads serialized PlanOut code and executes it. It's intended to be ported and run on top of an experimentation Platform. Currently it requires a compiler to convert from the PlanOut domain-specific language (see PlanOut paper).
  * PlanOutKit, an easy-to-use library for designing simple PlanOut experiments in python without the hassle of serializing experiments. It is recommended for students and those first learning how to implement experiments.
  * An Experiment class / framework for implementing and logging experiments that works with both PlanOut language and PlanOutKit experiments.

## PlanOutKit

Here is an example of how to use PlanOutKit. To create a PlanOutKit experiment, you subclass an ``Experiment`` object, and implement an execute method that includes all of your experiments inputs. Then, you construct a ``PlanOutKitMapper`` object that takes care of random assignment.  You can use PlanOut's random assignment operators by setting ``e.varname``, where ``e`` is the name of ``PlanOutKitMapper`` instance, and ``varname`` is the name of the variable you are setting.  Because we are using the ``SimpleExperiment`` class in the example below, the name of the experiment, ``FirstExperiment``, variable name, and the input data (``userid``) are used to perform the random assignment.  Parameter assignments and inputs are automatically logged into a file called ``firstexperiment.log'``.

```python

from planoutkit import *

class FirstExperiment(SimpleExperiment):
  def execute(self, userid):
    e = PlanOutKitMapper(self.salt)
    e.button_color = UniformChoice(choices=['#ff0000', '#00ff00'], unit=userid)
    e.button_text = WeightedChoice(
        choices=['Join now!', 'Sign up.'],
        weights=[0.3, 0.7], unit=userid)
    return e

my_exp = FirstExperiment(userid=12)
# parameters may be accessed via the . operator
print my_exp.get('button_text'), my_exp.get('button_color')

# experiment objects include all input data
for i in xrange(6):
  print FirstExperiment(userid=i)
```

which outputs (update this):

```
Sign up. #ff0000
{'inputs': {'userid': 0}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#00ff00', 'button_text': 'Sign up.'}}
{'inputs': {'userid': 1}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#ff0000', 'button_text': 'Join now!'}}
{'inputs': {'userid': 2}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#00ff00', 'button_text': 'Sign up.'}}
{'inputs': {'userid': 3}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#ff0000', 'button_text': 'Sign up.'}}
{'inputs': {'userid': 4}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#ff0000', 'button_text': 'Sign up.'}}
{'inputs': {'userid': 5}, 'salt': 'firstexperiment', 'name': 'firstexperiment', 'params': {'button_color': '#00ff00', 'button_text': 'Join now!'}}
```
