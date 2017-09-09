# ElectricField
Electric field lines and equipotentials using Runge-Kutta methods, including adaptive ones

A detailed description of the project is on the computational physics blog: http://compphys.go.ro/electric-field-lines/

### PROGRAM IN ACTION

[![Program video](https://img.youtube.com/vi/3JGs0VSAtqk/0.jpg)](https://youtu.be/3JGs0VSAtqk)

### TOOLS

The project compiles on Windows with Visual Studio 2015.

### LIBRARIES

mfc: included with Visual Studio

Direct2D: should already be there

### CLASSES

First, the classes generated by the MFC Application Wizard:

`CAboutDlg` - What the name says, I don't think it needs more explanation.

`CChildFrame` - It's a MDI application, this is the child frame. No change done to the generated class.

`CMainFrame` - Just a little bit of change to the generated one. The main one is the addition of the `OnViewOptions()` handler which displays the options property page.

`CElectricFieldDoc` - The document. Contains the 'field lines calculator' object. It's the base class for derived documents which implement particular charge configurations. Has a couple of `GetData` methods that deal with data retrieval and adjustment from the calculator and a `CheckStatus` that forwards the call to the calculator object, to check if the computing threads finished. The `OnCloseDocument` method called when a document is closed checks to see if threads were finished. If yes, the calculator object is deleted, if not, it is moved in the application object - after asking the threads for termination - where it stays until the threads are finished.

`CElectricFieldView` - The view class. It is changed to use Direct2D for displaying, except print preview and printing where it still uses gdi. The drawing is delegated to charges and field lines objects through the field object that is contained in the calculator (contained in the document). Has a timer set that allows checking for field lines data availability in the document.

`CElectricFieldApp` - The application class. Has minor changes and lots of document templates additions, one for each charge configuration from the program. Has a vector of 'calculators' that contains calculator objects from the documents that were closed before the threads finished. `ExitInstance` calls `OnIdle` until all threads are finished. `OnIdle` removes and deletes the calculator objects which have the computing threads finished.

The classes that deal with settings:

`Options` - This is the settings class. `Load` loads them from the registry, `Save` writes them into registry.

`OptionsPropertySheet`, `ComputationPropertyPage`, `DrawPropertyPage` - UI for the options. Not a big deal, should be easy to understand.

`PrecisionTimer` is a class that I used to measure some execution times. Not currently used in the program, it might be useful in some other projects, too.

`Vector2D<T>` - It's very similar with the Vector3D<T> class from the SolarSystem project, in fact I took it from there and changed it to be 2D, eliminating some code in the process. Obviously implements a vector in 2D, the current program is dealing with 2D ones.

`ComputationThread` - The base class for a field line computation thread. There is not much to it, `Start()` starts it.

`FieldLinesCalculator` - Contains the field object, the jobs queue, data from calculating threads, deals with the computing threads.

`FieldLinesCalculator::CalcThread` - The computing thread. Calculates electric field lines and equipotentials. Also posts equipotential jobs if it's the first electric field line/charge that it's calculating.

`TheElectricField` - The electric field class. Contains the charges and the electric field lines and equipotentials. Has methods to calculate the electric field and the potential and some other helper ones. Can draw itself by asking each charge and line to draw itself.

`Charge` - The charge class, has 'charge' and 'position'. Can draw itself and also can calculate the electric field and potential due of the charge it represents.

`FieldLine` - A field line. Instances of it are electric field lines. Contains the points and it has drawing methods and the Bezier adjustment code.

`PotentialLine` - Derived from the above to just contain the potential and the weight center.

The `RungeKutta` namespace contains the Runge-Kutta methods classes.

`RungeKutta<T, Stages>` - It is the base class, implements Runge-Kutta. The methods are derived from it.

`AdaptiveRungeKutta<T, Stages, Order>` - Adaptive Runge-Kutta. The adaptive methods are derived from it.

For the other ones, please see the code.

The `ChargeConfiguration` namespace contains classes derived from the document. They implement the particular charge configurations. 

### ADDING CHARGE CONFIGURATIONS

The first step would be to derive a class from `CElectricFieldDoc`. I put them all in the `ChargeConfiguration` namespace. Only `OnNewDocument` must be overridden. In there one adds the charges and then the calculation is started and that's about it. The second step is to add the document template in `CElectricFieldApp::InitInstance()`. The third step would be to add corresponding resources, string resource for names (as in IDR_DipoleTYPE `\nDipole\nDipole\n\n\n\n`), the menu resource (just copy the dipole one and change the id to whatever IDR_ you use) and icon. Be sure that the IDR_ id matches the one used for the document template.


