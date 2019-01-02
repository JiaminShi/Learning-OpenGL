# Learning-OpenGL

- Reference: https://learnopengl.com/

## Introduction
### State Machine
- context

### Object

## Before start
### GLFW & GLAD libraries
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

### Triangle
#### Graphics pipleline
-  The process of transforming 3D coordinates to 2D pixels is managed by the **graphics pipeline** of OpenGL
	-  3D coordinates -> 2D coordinates -> actual colored pixels
- All steps of pipeline can be executed in parallel
	- parallel nature <-> graphics cards with thousands of small processing cores to run small programs (**shaders**) on the GPU for each step of the pipeline
- can over
##### steps
- **vertex shader** 
	- input: a single vertex
	- transform 3D coordinates into different 3D coordinates
	- do some basic processing on the vertex attributes
-  primitive assembly
	-  i.e. vertices -> triangle
-  geometry shader
	-  input:  a collection of vertices 
	-  generate other shapes by emitting new vertices to form new (or other) primitive(s)
- rasterization stage (光栅)
	- maps the resulting primitive(s) to the corresponding pixels on the final screen
	-  Clipping: discards all fragments that are outside your view, increasing performance
-  **fragment shader**
	-  **fragment** is all the data required for OpenGL to render a single pixel.
	-   calculate the final color of a pixel 
	-   all the advanced OpenGL effects occur
- alpha test and blending

### Vertex input
- store the input data
##### vertex buffer objects (VBO)
	unsigned int VBO;
	glGenBuffers(1, &VBO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO); // bind object to state
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); 

- how the graphics card to manage the given data
	- GL_STATIC_DRAW: the data will most likely not change at all or very rarely.
	- GL_DYNAMIC_DRAW: the data is likely to change a lot.
	- GL_STREAM_DRAW: the data will change every time it is drawn

### Vertex shader
- shader language GLSL (OpenGL Shading Language)

i.e transform the input data to normalized device coordinates that fall within OpenGL's visible region
	
	#version 330 core
	// declare all the input vertex attribute
	layout (location = 0) in vec3 aPos;
	void main() {
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
	}

### Fragment shader

### Link Shader program

### Linking Vertex Attributes
tell OpenGL how to map the vertex data (input) in memory to the vertex shader's attributes (per vertex attribute)

	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0); // giving the vertex attribute location as its argument

- `0` specifies which vertex attribute we want to configure
	- recall that `layout (location = 0)`
- `3` specifies the size of the vertex attribute
	- The vertex attribute is a `vec3`.
- The third argument specifies the type of the data (a vec* in GLSL consists of floating point values)
- The next argument specifies if we want the data to be normalized
- The fifth argument is known as the **stride** and tells us the space between consecutive vertex attributes. 
	- since we know that the array is tightly packed, we could've also specified the stride as 0 to let OpenGL determine the stride (this only works when values are tightly packed).
- The last parameter is of type void* and thus requires that weird cast. This is the offset of where the position data begins in the buffer
	- Since the previously defined `VBO` is still *bound* before calling `glVertexAttribPointer`, vertex attribute 0 is now associated with its vertex data.


### Summary

#### VBO
repeat this process every time we want to draw an object. 

	// 0. copy our vertices array in a buffer for OpenGL to use
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	// 1. then set the vertex attributes pointers
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);  
	// 2. use our shader program when we want to render an object
	glUseProgram(shaderProgram);
	// 3. now draw the object 
	someOpenGLFunctionThatDrawsOurTriangle();   

#### VAO

	// ..:: Initialization code (done once ) :: ..
	// 1. bind Vertex Array Object
	glBindVertexArray(VAO);       /////<---------------
	// 2.(0. copy our vertices array in a buffer for OpenGL to use
	// 3.(1. then set our vertex attributes pointers  
	[...]
	// ..:: Drawing code (in render loop) :: ..
	// 4. draw the object
	glUseProgram(shaderProgram);
	glBindVertexArray(VAO);      /////<---------------
	someOpenGLFunctionThatDrawsOurTriangle();   

### Element Buffer Objects
-  indexed drawing: rectangle
- The glDrawElements function takes its indices from the EBO currently bound to the GL_ELEMENT_ARRAY_BUFFER target. This means we have to bind the corresponding EBO each time we want to render an object with indices 

#### Summary

	// ..:: Initialization code :: ..
	// 1. bind Vertex Array Object
	glBindVertexArray(VAO);
	// 2. copy our vertices array in a vertex buffer for OpenGL to use
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
	// 3. copy our index array in a element buffer for OpenGL to use
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
	// 4. then set the vertex attributes pointers
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);  

	[...]
  
	// ..:: Drawing code (in render loop) :: ..
	glUseProgram(shaderProgram);
	glBindVertexArray(VAO);
	glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0)
	// mode, the count or number of elements we'd like to draw, type of the indices, 
	// The last argument allows us to specify an offset in the EBO (or pass in an index array, but that is when you're not using element buffer objects)
	glBindVertexArray(0);