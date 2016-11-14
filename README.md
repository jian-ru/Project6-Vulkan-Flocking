Vulkan Flocking: compute and shading in one pipeline!
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 6**

* Jian Ru
  Windows 10, i7-4850 @ 2.3GHz 16GB, GT 750M 16GB (Personal)

### Q&A

* **Why do you think Vulkan expects explicit descriptors for things like
generating pipelines and commands? HINT: this may relate to something in the
comments about some components using pre-allocated GPU memory.**

To my understanding, you don't need a descriptor to generate commands. You can simply record commands using Vulkan API calls where you need to specify argument values and and the command buffer you want to record into. For generating pipelines and some other objects (e.g. descriptor sets, descriptor set layouts), Vulkan expects explicit descriptors because Vulkan is a low-level graphics API, instead of implicitly creating default pipeline(s) and providing you with function calls to configure them, it is the responsibility of programmers to explicitly state what we need or how we want things to be (e.g. what shader stages are enabled, wether or not to enable depth test, what is the depth test operator).

* Describe a situation besides flip-flop buffers in which you may need multiple
descriptor sets to fit one descriptor layout.

Descriptor set layouts specify in which shader stage, to which binding point of which target, there is something (e.g. buffer, texture) bound. Descriptor sets will further specify what is bound to each of these binding points (of various targets and shader stages). So whenever you need to bind different pieces of data to the same binding points, you can utilize multiple descriptor sets. For example, if I want to draw multiple models using the same set of shaders, I can create a descriptor set to hold the information of the vertex buffer of each model. Then I can just bind the corresponding descriptor set before I draw a different model rather than respecifying all the resource bindings and vertex data layouts again. It is kind of like VAO in OpenGL but now you actually hold a structure of descriptor set that contains all the meta information rather than a simple GLuint VAO handle.

* What are some problems to keep in mind when using multiple Vulkan queues?
  * take into consideration that different queues may be backed by different hardware
  * take into consideration that the same buffer may be used across multiple queues
  
  According to the hints, the caveats I can imagine are hardware compatibility and synchronization. Since different queues may be backed by different hardware, we need to ensure the commands we use are supported if they will be executed in multiple queues. We also need to explicitly specify the correct queue family indices when creating memory barriers set between shaders on different queues. If a resource (e.g. buffers, textures) are used accross different queues, proper synchronization using memory barriers and fences is required.
  
* What is one advantage of using compute commands that can share data with a
rendering pipeline?

Avoid the super-duper expensive context switch!!! For example, switching between CUDA context and OpenGL context. A context is like a process on CPU.

### Credits

* [Vulkan examples and demos](https://github.com/SaschaWillems/Vulkan) by [@SaschaWillems](https://github.com/SaschaWillems)
