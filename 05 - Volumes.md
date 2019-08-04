# Part 1 : Shortcuts

Talking about Volume, in general, the storage that's internal to your containers is ephemeral as it is designed to be temporary, as long as the container is stopped or destroyed, any internal storage is completely removed. What the volumes do is to allow us to provide some sort of more permanent external storage to our pods and their containers. This storage exists outside the life of the container, and therefore it can continue on even if the the container stops or is replaced. 

### 1. Create PV :

  
