## Table of Contents
- [Introduction](#introduction)
- [Flow Calculation](#flow-calculation)
  - [User Input](#user-input)
  - [Unit Conversion](#unit-conversion)
  - [Interfacing Python and JavaScript](#Interfacing-with-Python-and-JavaScript)
  - [Output Graph](#output-graph)
- [Flow Templates](#Flow-Templates)
- [How to Add an Input](#how-to-add-an-input)
- [How to Add an Output](#how-to-add-an-output)
- [Changing Python Code](#changing-python-code)

## Introduction
Web Calculator for Andrew A Oliva and Scott C Morris's Quasi Steady, quasi-one-dimensional, internal compressible flow with area change, heat addition and friction
Author: Ben Boardley
## Calculation Work Flow

![Flow Diagram](flow-charts/calculate-flow.jpg)

### Unit Conversion
Action: User changes output unit
```
userChangedOutputUnit 
|
|convertFromSI
|
|assignOutputs
```
Action: User changes input unit
```
calcualteFlow
|
|assignInputs
|
|convertToSI
```
#### Interfacing with Python and JavaScript

![Tech Stack](flow-charts/TechStack.png)
The Interface with the python code occurs in InterfaceUNI in the JavaScript Code.

JavaScript Integrates with python through interaction with global objects:
   ```
    uniflow = pyscript.interpreter.globals.get('main');
    uniflow();
    js_args = pyscript.interpreter.globals.get('args');
  ```
pyscript.interpreter.globals.get can grab any global variable, global function, or global class in python and import it to javascript.
Java script can retrieve arrays from the argys class through  `output.M2 = js_args.M2.toJs();` -- TOJS() converts python arrays into javascript ones. If it is not an array you can just use `output.Lambda = js_args.Lambda;` Notice how the objects are retrieved with the .VariableName. This is because it matches the variable names in the args python class. For example here are some, but not all of the names. The args class is equivalent to the args class in your original python code.
```
class Args:
        def __init__(self, AR=1.0, Ad=0.0, cd=0.0, cf=0.0, Af=0.0, M1=0.0, xi=0.5, nu=0.0,
                    qr=0.0, k=1.4, twr=1.0, isentropic=False, b1=1.0, b2=1.0, a1=1.0, a2=1.0, N=1, debug=False):
            self.AR = AR
            self.Ad = Ad
            self.cd = cd
            self.cf = cf
            self.Af = Af
            self.M1 = M1
            self.xi = xi
            self.nu = nu
            self.qr = qr
            self.k = k
            self.twr = twr
            self.isentropic = isentropic
            self.b1 = b1
            self.b2 = b2
            self.a1 = a1
            self.a2 = a2
            self.debug = True
            self.N = N
            self.error = None
```

#### javascript to python
Java Script to python is a little simpler. In this the data object from javascript is imported, data contains all inputs. Again this has to be a global variable. The data object is the inputs from the web calculator.
```
    def main():
      global args
      from js import data
```

#### Pyscript
Pyscript is used to bring python to the front end of the application. That way we do not have to have a backend server. This is used in two componenets.
1. 
 ```
   <py-config>
    packages = ["numpy", "scipy", "tqdm"] 
  </py-config>
  ```
  py-config labels the packages that will be imported that are not apart of the default python library (e.g. sys )
2. 
  ```
  <py-script>
    **insert python code**
  </py-script>
  ```
  This is the python code that will be ran when it is interfaced with the javascript.

Note:
  Pyscript is still under development so there may be small changes that have to be made to the tags or structure as it progresses. These should be small. One example of a recent change was this html code:
  ```
    <py-config>
      packages = ["numpy", "scipy", "tqdm"] 
    </py-config>
  ```
  used to be this:
  ```
    <py-env>
      - numpy
      - scipy
      - tqdm
    </py-env>
  ```

### Output Graph
![Flow Diagram](flow-charts/output-graph.jpg)

This process is at the bottom of the script file.

### Flow Templates
Based upon the selected templates the default input values get assigned accordingly. If you want to make it so the box goes gray and can't be 
edited when a template is selected, you would add lines like this:
```
      document.getElementById("A2").setAttribute('readonly', true);
      document.getElementById("A2").style = "background-color: gray;";
```
In which "A2" comes from the html id `<input type="text" id="A2" oninput="imageGUI()">` in this section.
```
                    <div class="label-output-row" title="Outlet Area">
                        <label for="A2">A<sub>2</sub>=</label>
                        <input type="text" id="A2" oninput="imageGUI()">
                        <select id="outlet-area-unit" onchange="imageGUI()">
                            <option value="meters">m&sup2;</option>
                            <option value="centimeters">cm&sup2;</option>
                            <option value="feet">ft&sup2;</option>
                            <option value="inches">in&sup2;</option>
                        </select>
                    </div>
```

## How to Add an Input
1. add to HTML section in either inlet, parameters or outlet:

  ```
                    <!-- Input -->
                    <div class="label-output-row" title="Inlet Stagnation Temperature">
                        <label for="To1">T<sub>o<sub>1</sub></sub>=</label>
                        <input type="text" id="To1">
                        <select id="input-temp-unit">
                            <option value="kelvin">K&deg;</option>
                            <option value="celsius">C&deg;</option>
                            <option value="rankine">R&deg;</option>
                            <option value="farenheit">F&deg;</option>
                        </select>
                    </div>
  ```
2. add to assignInputs in script make sure to use id. the || 0 is incase of the user not putting an input:
```
  function assignInputs() {
  data.po1 = parseFloat(document.getElementById("Po1").value) || 0;
  data.R = parseFloat(document.getElementById("R").value) || 0;
  data.to1 = parseFloat(document.getElementById("To1").value) || 0;
  data.cd = parseFloat(document.getElementById("cd").value) || 0;
  data.cf = parseFloat(document.getElementById("cf").value) || 0;
  data.qr = parseFloat(document.getElementById("qr").value) || 0;
  data.Af = parseFloat(document.getElementById("Af").value) || 0;
  data.Ad = parseFloat(document.getElementById("Ad").value) || 0;
  data.M1 = parseFloat(document.getElementById("M1").value) || 0;
  data.A1 = parseFloat(document.getElementById("A1").value) || 0;
  data.gamma = parseFloat(document.getElementById("gamma").value) || 0;
  data.alpha1 = parseFloat(document.getElementById("alpha1").value) || 0;
  data.beta1 = parseFloat(document.getElementById("beta1").value) || 0;
  data.A2 = parseFloat(document.getElementById("A2").value) || 0;
  data.xi = parseFloat(document.getElementById("xi").value) || 0;
  data.nu = parseFloat(document.getElementById("nu").value) || 0;
  data.alpha2 = parseFloat(document.getElementById("alpha2").value) || 0;
  data.beta2 = parseFloat(document.getElementById("beta2").value) || 0;
  data.N = parseFloat(document.getElementById("subelements").value) || 0;
  data.isent = document.getElementById("isentropic").value;
  convertToSI();
}

```
3. Add a default value to the top of example flows. Then add any other template value.
4. If a Dimensional Input add to converttoSI:
```
  var P1 = parseFloat(document.getElementById("Po1").value) || 0;
  var pressureUnit = document.getElementById("pressure-unit").value;

    switch(pressureUnit) {
      case 'kpascals':
          data.po1 = kpascalsToPascals(P1);
          break;
      case 'psi':
        data.po1 = psiToPascals(P1);
          break;
      case 'psf':
        data.po1 = psfToPascals(P1);
          break;
  }

```
5. add to python code in html `<py-script>` tags:
    implement it into args so that it can be pulled from your uniflow code.
    ```
        class Args:
        def __init__(self, AR=1.0, Ad=0.0, cd=0.0, cf=0.0, Af=0.0, M1=0.0, xi=0.5, nu=0.0,
                    qr=0.0, k=1.4, twr=1.0, isentropic=False, b1=1.0, b2=1.0, a1=1.0, a2=1.0, N=1, debug=False):
            self.AR = AR
            self.Ad = Ad
            self.cd = cd
            self.cf = cf
            self.Af = Af
            self.M1 = M1
            self.xi = xi
            self.nu = nu
            self.qr = qr
            self.k = k
            self.twr = twr
            self.isentropic = isentropic
            self.b1 = b1
            self.b2 = b2
            self.a1 = a1
            self.a2 = a2
            self.debug = True
            self.N = N
            self.error = None

        args = Args(AR=data.A2/data.A1, Ad = data.Ad/data.A1, Af = data.Af/data.A1, qr = data.qr, cd = data.cd, cf = data.cf, M1=data.M1, xi = data.xi, nu = data.nu, k = data.gamma, b1 = data.beta1, b2 = data.beta2, a1 = data.alpha1, a2 = data.alpha2 , N = data.N, isentropic=(data.isent == 'True'))
    ```
6.  If you want it to be graphed add it into this selection section. Either y or x for output, input respectivley.

  One thing to note is that it is decided based upon the selected values also make sure to note that the option values. `value="M2"` is the same as the variable name in output or data `output.M2`. The actual text for the option does not matter.
  ```
  <h4>Output Charts</h4>
  <select class="selection-component" id="chart-y" onchange="outputGraph()">
  <option value="M2">M&#8322; <!-- M subscript 2 --></option>
  <option value="P2P1">P&#8322;/P&#8321; <!-- P subscript 2 divided by P subscript 1 --></option>
  <option value="T2T1">T&#8322;/T&#8321; <!-- T subscript 2 divided by T subscript 1 --></option>
  <option value="TR">To&#8322;/To&#8321; <!-- To subscript 2 divided by To subscript 1 --></option>
  <option value="PR">Po&#8322;/Po&#8321; <!-- Po subscript 2 divided by Po subscript 1 --></option>
  <option value="dsR">&Delta;s/R</option>
  <option value="M2M1">M&#8322;/M&#8321; <!-- M subscript 2 divided by M subscript 1 --></option>
  <option value="dsRKE">(ds/R)/(k/2*M&#8321;&sup2;) <!-- (delta s divided by R) divided by (k divided by 2 times M subscript 1 squared) --></option>
  <option value="wloss">(Po&#8321;-Po&#8322;)/(Po&#8321;-P&#8321;) <!-- (Po subscript 1 minus Po subscript 2) divided by (Po subscript 1 minus P subscript 1) --></option>
  <option value="cp">(P&#8322;-P&#8321;)/(Po&#8321;-P&#8321;) <!-- (P subscript 2 minus P subscript 1) divided by (Po subscript 1 minus P subscript 1) --></option>
  <option value="T2">T&#8322; <!-- T subscript 2 --></option>
  <option value="P2">P&#8322; <!-- P subscript 2 --></option>
  <option value="to2">To&#8322; <!-- T subscript 2 o subscript 2 --></option>
  <option value="po2">Po&#8322; <!-- P subscript 2 o subscript 2 --></option>
  <option value="ds">&Delta;s</option>
  </select>

  <h4 style="margin-top: 0px;">vs</h4>

  <select class="selection-component" id="chart-x" onchange="outputGraph()">
  <option value="M1">M&#8321; <!-- M subscript 1 --></option>
  <option value="A1">A&#8321; <!-- A subscript 1 --></option>
  <option value="gamma">&#947; <!-- Gamma --></option>
  <option value="cd">c&#8322;d <!-- c subscript d --></option>
  <option value="cf">c&#8322;f <!-- c subscript f --></option>
  <option value="twr">TWR</option>
  <option value="alpha1">&#945;&#8321; <!-- Alpha subscript 1 --></option>
  <option value="beta1">&#946;&#8321; <!-- Beta subscript 1 --></option>
  <option value="qr">q&#8322;r <!-- q subscript r --></option>
  <option value="Af">A&#8322;f <!-- A subscript f --></option>
  <option value="Ad">A&#8322;d <!-- A subscript d --></option>
  <option value="A2">A&#8322; <!-- A subscript 2 --></option>
  <option value="xi">&xi; <!-- Xi --></option>
  <option value="nu">&nu; <!-- Nu --></option>
  <option value="alpha2">&alpha;&#8322; <!-- Alpha subscript 2 --></option>
  <option value="beta2">&beta;&#8322; <!-- Beta subscript 2 --></option>
  </select>
  ```
## How to Add an Output
1. Add to output section in HTML into either traditional-output or additional-output and then non-dimensional or dimensional:
```
                    <!-- Output for P2P1 -->
                    <div class="label-output-row" title="Pressure ratio">
                        <label for="P2P1">P<sub>2</sub>/P<sub>1</sub>=</label>
                        <input type="text" id="P2P1">
                        <input type="text" id="P2P1Second">
                        <select><option>-</option></select>
                    </div>
```
2. add to interfaceUNI or calculateRootsWithUnits:

    interfaceUNI if array:
    ```
        output.P2P1 = js_args.P2P1.toJs();
    ```
    if not array:
       ```
        output.P2P1 = js_args.P2P1;
        ```
    if units add to calculateRootsWithUnits:
    ```
    output.P2 = []
    output.P2[0] = output.P1 * output.P2P1[0]
    output.P2[1] = output.P1 * output.P2P1[1]
    ```

3. Add to assignOutputs:
    ```
    document.getElementById("P2P1").value = output.P2P1[0].toFixed(5);
    document.getElementById("P2P1Second").value = output.P2P1[1].toFixed(5);
    ```

4.  If a Dimensional Input add to convertFromSI make sure this is also in calculateRootswithUnits:
```
  var pressureUnit = document.getElementById("static-pressure1-unit").value;

  switch (pressureUnit) {
    case 'kpascals':
      output.P1 = pascalsToKpascals(output.P1);
      break;
    case 'psi':
      output.P1 = pascalsToPsi(output.P1);
      break;
    case 'psf':
      output.P1 = pascalsToPsf(output.P1);
      break;
  }

``` 
5. If you want it to be graphed add it into this selection section. Either y or x for output, input respectivley.

  One thing to note is that it is decided based upon the selected values also make sure to note that the option values. `value="M2"` is the same as the variable name in output or data `output.M2`. The actual text for the option does not matter.
  ```
  <h4>Output Charts</h4>
  <select class="selection-component" id="chart-y" onchange="outputGraph()">
  <option value="M2">M&#8322; <!-- M subscript 2 --></option>
  <option value="P2P1">P&#8322;/P&#8321; <!-- P subscript 2 divided by P subscript 1 --></option>
  <option value="T2T1">T&#8322;/T&#8321; <!-- T subscript 2 divided by T subscript 1 --></option>
  <option value="TR">To&#8322;/To&#8321; <!-- To subscript 2 divided by To subscript 1 --></option>
  <option value="PR">Po&#8322;/Po&#8321; <!-- Po subscript 2 divided by Po subscript 1 --></option>
  <option value="dsR">&Delta;s/R</option>
  <option value="M2M1">M&#8322;/M&#8321; <!-- M subscript 2 divided by M subscript 1 --></option>
  <option value="dsRKE">(ds/R)/(k/2*M&#8321;&sup2;) <!-- (delta s divided by R) divided by (k divided by 2 times M subscript 1 squared) --></option>
  <option value="wloss">(Po&#8321;-Po&#8322;)/(Po&#8321;-P&#8321;) <!-- (Po subscript 1 minus Po subscript 2) divided by (Po subscript 1 minus P subscript 1) --></option>
  <option value="cp">(P&#8322;-P&#8321;)/(Po&#8321;-P&#8321;) <!-- (P subscript 2 minus P subscript 1) divided by (Po subscript 1 minus P subscript 1) --></option>
  <option value="T2">T&#8322; <!-- T subscript 2 --></option>
  <option value="P2">P&#8322; <!-- P subscript 2 --></option>
  <option value="to2">To&#8322; <!-- T subscript 2 o subscript 2 --></option>
  <option value="po2">Po&#8322; <!-- P subscript 2 o subscript 2 --></option>
  <option value="ds">&Delta;s</option>
  </select>

  <h4 style="margin-top: 0px;">vs</h4>

  <select class="selection-component" id="chart-x" onchange="outputGraph()">
  <option value="M1">M&#8321; <!-- M subscript 1 --></option>
  <option value="A1">A&#8321; <!-- A subscript 1 --></option>
  <option value="gamma">&#947; <!-- Gamma --></option>
  <option value="cd">c&#8322;d <!-- c subscript d --></option>
  <option value="cf">c&#8322;f <!-- c subscript f --></option>
  <option value="twr">TWR</option>
  <option value="alpha1">&#945;&#8321; <!-- Alpha subscript 1 --></option>
  <option value="beta1">&#946;&#8321; <!-- Beta subscript 1 --></option>
  <option value="qr">q&#8322;r <!-- q subscript r --></option>
  <option value="Af">A&#8322;f <!-- A subscript f --></option>
  <option value="Ad">A&#8322;d <!-- A subscript d --></option>
  <option value="A2">A&#8322; <!-- A subscript 2 --></option>
  <option value="xi">&xi; <!-- Xi --></option>
  <option value="nu">&nu; <!-- Nu --></option>
  <option value="alpha2">&alpha;&#8322; <!-- Alpha subscript 2 --></option>
  <option value="beta2">&beta;&#8322; <!-- Beta subscript 2 --></option>
  </select>
  ```
## Changing Python Code
Can copy and past everything from python into the pyscript tags (tqdm does not work). However, I would not copy and paste the main.py or the Args class
these I would recommend changing manually because the args class and main function are the only things that differ from uniflow.py. 
e.g. differences between mains:
```
pyscript:
    def main():
      global args
      from js import data
      ver = str('v2.2')
      date = str('06/21/23')
      
      args = Args(AR=data.A2/data.A1, Ad = data.Ad/data.A1, Af = data.Af/data.A1, qr = data.qr, cd = data.cd, cf = data.cf, M1=data.M1, xi = data.xi, nu = data.nu, k = data.gamma, b1 = data.beta1, b2 = data.beta2, a1 = data.alpha1, a2 = data.alpha2 , N = data.N, isentropic=(data.isent == 'True'))
      try:
        if( args.isentropic ):
            args = isentropic(args)
            args = coefsBiquadratic(args)

        else:
            if( args.N  == 1 ):
                args = coefsBiquadratic(args)
                args = solveBiquadratic(args)
            else:
                args = subelements(args)

        args = postProcess(args)
      except Exception as e:
        args.error = e
      return()
```
```
uniflow:
def main():
    ver = str('v2.2')
    date = str('06/21/23')
    intro('UNIfied steady quasi-1d compressible FLOW',ver,date)

    args = getArgs()
    regurgitate(args)

    if( args.isentropic ):
        args = isentropic(args)
        args = coefsBiquadratic(args)

    else:
        args = subelements(args)

    args = postProcess(args)

    printOutput(args)

    return()
```
## Also NOTE tqdm does not work in pyscript!
