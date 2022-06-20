## SLIGHTLY OUT OF DATE, ILSPY IS LARGELY PREFERRED TO DNSPY DUE TO BETTER DECOMPILATION QUALITY #
https://github.com/icsharpcode/ILSpy/releases/

For linux users, take the avalonia version : https://github.com/icsharpcode/AvaloniaILSpy/releases

# Guide #

[Latest Release](https://github.com/0xd4d/dnSpy/releases)

> dnSpy is a debugger and .NET assembly editor. You can use it to edit and debug assemblies even if you don't have any source code available.

## Getting to grips with DnSpy

Extract & open DnSpy.exe from the downloaded archive and then hit File->Open->Select \Risk of Rain 2\Risk of Rain 2_Data\Managed\RoR2.dll. You should be presented with something like this:
![Opened DLL](https://i.imgur.com/5F3mZ20.png)

You can explore the Namespace hierarchy in the left pane. For instance we can take a look through the RoR2.Run class by selecting it in the left pane.
![Namespace hierarchy](https://i.imgur.com/nDxO0BW.png)

You can search for specific variables, methods, and types by using the "Search Assemblies" function.
![Search Assemblies](https://i.imgur.com/MmhYmft.png)

You can also search for references to a specific property/field by using the Analyse function.
![Reference property](https://i.imgur.com/IswRix1.png)
![Reference propert2](https://i.imgur.com/1OaQKhU.png)

It is possible to edit the method that you are viewing, which can be useful for generating the correct IL code within the current context
![Edit method](https://i.imgur.com/PYQhd2h.png)

You can then view the corresponding IL code to your modified method
![IL viewer](https://i.imgur.com/gKMSrH0.png)
If you use ILSpy, you need to switch to IL view here
![ILSpy IL View](https://cdn.discordapp.com/attachments/562704639569428506/962043759120515102/unknown.png)

With only the functions listed above, it becomes relatively easy to start to understand the codebase that you are working with, and to decide upon a course of action how you intend to accomplish a certain task.