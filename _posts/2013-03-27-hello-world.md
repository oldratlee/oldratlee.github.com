---
layout: post
title: Hello World!
location: Hangzhou
tags:
- test
- hello.world
---
For me, StyleCop is a valuable element in the C# toolchain. It helps to maintain a consistent coding style within a given context, be it a single file, project, solution, a dev team, an entire organisation or, potentially, even the entire C# industry.

Until now, StyleCop has been made available principally as a Visual Studio add-in which can be invoked on demand by a developer - see [the documentation](https://stylecop.codeplex.com/documentation) for more details.

It is also possible to run StyleCop as part of the build process - see ['Setting Up StyleCop MSBuild Integration'](https://stylecop.codeplex.com/wikipage?title=Setting%20Up%20StyleCop%20MSBuild%20Integration). As the documentation shows, it is even possible to add the StyleCop binaries to a source code repository and have the analysis run whenever and wherever the assemblies are built. The advantage of this approach is that StyleCop does not need to be installed on every machine where the assemblies are built and is therefore guaranteed to run during every build.

To set this up, a number of manual steps are involved:-

1. Install the add-in, choosing the option to install MSBuild files (or grab the binaries and targets from elsewhere)
1. Copy the StyleCop binaries and targets files into your source code repository
1. Add &lt;Import&gt; elements to your csproj files pointing to the StyleCop targets file

(There are variations on this theme such adding an environment variable pointing to the targets file to make csproj file maintenance easier.)

<a name="more"></a>

As I'm sure the astute reader is already asking, wouldn't it be good if all this were automated?

<!--excerpt-->

##Enter StyleCop.MSBuild

StyleCop.MSBuild is a [Nuget package](http://nuget.org/packages/StyleCop.MSBuild) which automates this process. In order to add MSBuild-integrated StyleCop analysis to your projects, simply install the StyleCop.MSBuild Nuget package in the normal way using either the Package manager console or GUI. The installation script will take care of adding the relevant <Import> elements to your csproj files (uninstallation is also supported).

StyleCop.MSBuild installs exactly the same binaries as those contained in [the official StyleCop release](https://stylecop.codeplex.com/). Whenever a new version of StyleCop becomes available, a new version of StyleCop.MSBuild will be released alongside with a corresponding version number. To update to the new version, simply update your Nuget packages in the usual way and the StyleCop.MSBuild installation script will take care of updating the <Import> elements in your csproj files.

(Note that the 'StyleCop.MSBuild' Nuget package is not to be confused with the 'StyleCop' Nuget package, which is intended for referencing as a library for writing custom StyleCop rules.)

##For current users of versions 4.7.14.0 or 4.7.17.0

If you are already using StyleCop.MSBuild version 4.7.14.0 or 4.7.17.0 then updating to 4.7.17.1 or higher will not work. Those versions simply added the binaries to your source code repository and did not affect your projects in any way which means that Nuget doesn't recognise them as being installed in any projects, only the solution. Instead of *updating* from either of these versions, *remove* them and then *install* version 4.7.17.1 or higher.

##Credits

Thanks to eddie_butt @ codeplex and Amy Dullard @ Microsoft for suggesting the addition of the auto-import feature. Thanks to Andy Reeves @ StyleCop for offering to pull the package code into the main project (when either I or someone else get round to providing a patch!).

##Source

The source code for this package is currently hosted at...

It is planned that this will be merged into [the main StyleCop project at CodePlex](https://stylecop.codeplex.com/) at some point in the future.