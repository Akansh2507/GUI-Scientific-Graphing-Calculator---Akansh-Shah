# GUI-Scientific-Graphing-Calculator---Akansh-Shah
https://pythonprogramming.net/how-to-embed-matplotlib-graph-tkinter-gui/
https://medium.com/@rahulmallah785671/build-your-own-scientific-calculator-with-python-a-step-by-step-guide-15bbf0419325
https://pythonbasics.org/tkinter-label/
https://www.geeksforgeeks.org/python-grid-method-in-tkinter/
https://stackoverflow.com/questions/63852411/displaying-plot-in-tkinter-gui-frame
https://stackoverflow.com/questions/70967450/how-to-use-tkinter-buttons-to-help-in-calculator
https://www.geeksforgeeks.org/how-to-embed-matplotlib-charts-in-tkinter-gui/ 
https://pythonprogramming.net/how-to-embed-matplotlib-graph-tkinter-gui/
https://stackoverflow.com/questions/944700/how-to-check-for-nan-values
https://stackoverflow.com/questions/2244270/get-a-try-statement-to-loop-around-until-correct-value-obtained
https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.axhline.html
https://stackoverflow.com/questions/16930328/vertical-horizontal-lines-in-matplotlib
https://www.w3schools.com/python/ref_func_getattr.asp
https://stackoverflow.com/questions/15781802/python-tkinter-clearing-a-frame

import tkinter as tk #this imports the library for the GUI
import math #this imports the library for the math operations 
import numpy as np #this imports numpy as an array 
import matplotlib #this imports the function for graphing 
matplotlib.use("TkAgg") #this makes the matplotlib plots work
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg #this displays the matplotlib plots
from matplotlib.figure import Figure #this imports figures to create plots

class Calculator: #this handles the GUI calculator 
    def __init__(self, master): #this initializes the calculator object
        self.master = master #this assigns the main window to self.master
        master.title("Calculator") #this sets the title of the window
        master.geometry("400x600") #this sets the deminsions of the window

        self.total = tk.StringVar() #this creates a string variable to hold the value of the calculator 
        
        self.entry = tk.Entry(master, textvariable = self.total, font = ("Calibri", 14)) #this creates a widget for the input and display of results
        self.entry.grid(row = 0, column = 0, columnspan = 5, pady = 5) #this positions the entry grid 

        self.make_buttons() #this calls the function to create calculator buttons

        self.label = tk.Label(master, text="EXAMPLES: x**2, sin(x), cos(x), tan(x), x**3 - 5*x + 4", font = ("Helvetica", 14)) #this labels the example equations 
        self.label.grid(row = 6, column = 0, columnspan = 5, pady = 2) #this positions the perticular label
        
        self.plot_frame = tk.Frame(master) #this creates a frame to hold the plot 
        self.plot_frame.grid(row = 8, column = 0, columnspan = 5) #this positions the frame 

    def make_buttons(self): #this creates the calculator buttons 
        button_list = [ #this is a 2D list of the buttons 
            ['sin', 'cos', 'tan', '^', 'sqrt'],
            ['7', '8', '9', '/', '('],
            ['4', '5', '6', '*'],
            ['1', '2', '3', '-'],
            ['0', 'AC', '=', '+']
        ]

        for i, row in enumerate(button_list): #this loops through the list to create and position buttons
            for j, text in enumerate(row): 
                button = tk.Button(self.master, text = text, width = 5, height = 2, font = ("Calibri", 14),
                                   command = lambda text = text: self.click_button(text)) #this creates a button with a label and a command that calls click_button when pressed 
                button.grid(row = i + 1, column = j, sticky = "nsew", padx = 2, pady = 2) #this positions each button 

        button_1 = tk.Button(self.master, text = 'Plot', width = 10, height = 2, command = self.plot_graph) #this creates a plot button to plot graphs 
        button_1.grid(row = 6, column = 4, sticky = "nsew", padx = 2, pady = 2) #this positions the plot button 
        button_1['font'] = ("Calibri", 14) #this sets the font for the plot button 

        for i in range(5): #this makes the columns based on the window sizing 
            self.master.columnconfigure(i, weight = 1) 

    def click_button(self, text): #this method handles button clicks 
        if text == '=': #this evaluvates the expression
            try:
                result = eval(self.entry.get()) #this evaluvates the math expression 
                self.total.set(result) #this displays the result 
            except:
                self.total.set("Error") #this handles the errors in the expression 

        elif text == 'AC': #this clears the entry if AC is pressed 
            self.total.set("") 

        elif text == 'sqrt': #this sets a conditon if the square root button is pressed 
            try:
                result = math.sqrt(float(self.entry.get())) #this computes the square root 
                self.total.set(result) 
            except:
                self.total.set("Error") #this handles the errors 

        elif text in ['sin', 'cos', 'tan']: #this calculates the trig functions and displays the results 
            try:
                result = getattr(math, text)(math.radians(float(self.entry.get()))) #this converts the input to radians 
                self.total.set(result)
            except:
                self.total.set("Error") #this handles the errors 

        elif text == '^': #this handles the power function or symbol 
            value = self.entry.get() 
            self.total.set(value + "**") #this replaces '^", with '**' for python to understand 
        else:
            self.total.set(self.entry.get() + text) #this appends the other pressed buttons to the current expression 

    def plot_graph(self): #this defines a function to plot the graph 
        for widget in self.plot_frame.winfo_children(): #this is to remove or clear any previous plotted graph 
            widget.destroy() 

        fig = Figure(figsize = (5, 5), dpi = 100) #this creates a new figure for the plot 

        try:
            x_values = np.linspace(-10, 10, 1000) #this generates the x values for the plot 
            equation = self.entry.get() #this gets the equation from the entry 

            equation = equation.replace('sin', 'np.sin').replace('cos', 'np.cos').replace('tan', 'np.tan') #this replaces sin, cos, and tan with numpy values

            y_values = eval(equation, {"x": x_values, "np": np, "math": math}) #this evaluvates the equation to get y values 

            if 'np.tan' in equation: #this handles the asymptotes in the tan graph 
                y_values[np.abs(y_values) > 10] = np.nan #this replaces large values with nan and to avoid tan lines from intersecting 

            plot1 = fig.add_subplot(111) #this plots the graph and creates a subplot 
            plot1.plot(x_values, y_values, label = equation) #this plots the equation 
            plot1.axhline(0, color = 'black', lw = 0.5) #this draws the x axis  
            plot1.axvline(0, color = 'black', lw = 0.5) #this draws the y axis 
            plot1.set_title("Graph of " + equation) #this sets the titles 
            plot1.legend() #this sets the legend 
            plot1.grid(visible = True) #this displays the grid 

            canvas = FigureCanvasTkAgg(fig, master = self.plot_frame) #this plots into the Tkinter window  
            canvas.draw() #this draws the plot

            canvas.get_tk_widget().pack() #this puts the canvas into the frame 

        except Exception as e: #this handles the errors during plotting 
            self.total.set(f"Plot Error: {e}")

if __name__ == '__main__': #this main function runs the calculator app 
    root = tk.Tk() #this creates the main window 
    my_calculator = Calculator(root) #this creates the caculator 
    root.mainloop() #this runs the loop of Tkinter
