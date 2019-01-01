# Learning-OpenGL

- Reference: https://learnopengl.com/

## State Machine
- context

## Object

## GLFW & GLAD libraries
https://learnopengl.com/Getting-started/Creating-a-window
- GLFW: 
  - a library written in C 
  - allows us to create an OpenGL context, define window parameters and handle user input
- GLAD:
  - retrive graphics driver related info
- [linker errors](https://stackoverflow.com/questions/31736361/visual-studio-2015-gives-me-errors-upon-creating-a-simple-test-console-program)

## Window
- register callback function:
	- after creating the window and before the game loop is initiated
- screen clear
	- `glClearColor` is a state-setting functio
	- `glClear` is a state-using function in that it uses the current state to retrieve the clearing color from.
	
