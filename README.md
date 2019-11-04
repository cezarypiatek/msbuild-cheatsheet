# MsBuild cheatsheet

## Properties

### Define property

```xml
<PropertyGroup>
    <SampleProperty>Value</SampleProperty>
</PropertyGroup>
```

## Collections

### Define collection

```xml
<ItemGroup>
    <SampleCollection Include="Element1">
        <SampleMetadata>ValueX</SampleMetadata>
    </SampleProperty>
    <SampleCollection Include="Element2">
        <SampleMetadata>ValueY</SampleMetadata>
    </SampleProperty>
</ItemGroup>
```

### Update metadata

```xml
<ItemGroup>
    <SampleCollection Update="@(SampleCollection)"  Condition="'%(SampleMetadata)' == 'ValueX'">
        <SampleMetadata>ValueZ</SampleMetadata>
    </SampleProperty>  
</ItemGroup>
```

### Remove elements

```xml
<ItemGroup>
    <SampleCollection Remove="@(SampleCollection)" Condition="'%(SampleMetadata)' == 'ValueX'" />
</ItemGroup>
```

### Remove duplicates

```xml
<ItemGroup>
    <MyItems Include="MyFile.cs"/>
    <MyItems Include="MyFile.cs">
        <Culture>fr</Culture>
    </MyItems>
    <MyItems Include="myfile.cs"/>
</ItemGroup>

<Target Name="RemoveDuplicateItems">
    <RemoveDuplicates
        Inputs="@(MyItems)">
        <Output
            TaskParameter="Filtered"
            ItemName="FilteredItems"/>
    </RemoveDuplicates>
</Target>
```

### Write elements to file

```xml
<Target Name="WriteCollectionToFile">
    <WriteLinesToFile File="SampleFile.txt" Lines="@(SampleCollection)" Overwrite="true" />
</Target>
```

### Read elements from file

```xml
<Target Name="ReadCollectionFromFile">
    <ReadLinesFromFile File="SampleFile.txt">
        <Output TaskParameter="Lines" ItemName="SampleCollection"/>    
    </ReadLinesFromFile>
</Target>
```

## Targets

### Call another target

```xml
<Target Name="Start">
    <CallTarget Targets="AnotherTarget" />
</Target> 
```

### Incremental build

The target will be skipped if the output file exists is newer than all of the input files.

```xml
<Target Name="SampleTarget" Inputs="@(InputFiles)" Outputs="$(OutputFile)">
</Target>
```

### Run target for each element

```xml
<Target Name="SampleTarget" Inputs="@(SampleCollection)" Outputs="%(Identity).Dummy">
    <Message Text="Item = %(SampleCollection.Identity)" />
    <Message Text="Meta = %(SampleCollection.SampleMetadata)" />
</Target>
```

### Running target if file get changed
This will run `GenerateCodeFromAttributes` target if any files that belongs to `Compile` collection get changed.

```xml
<ItemDefinitionGroup>
    <Compile>
        <Generator>MSBuild:GenerateCodeFromAttributes</Generator>
    </Compile>
</ItemDefinitionGroup>
```


## Tasks

### Using task

```xml
<UsingTask TaskName="NuGet.Build.Tasks.RestoreTask" AssemblyFile="NuGet.Build.Tasks.dll" />
<Target Name="Restore" DependsOnTargets="_GenerateRestoreGraph">
    <RestoreTask
      RestoreGraphItems="@(_RestoreGraphEntryFiltered)"
      RestoreDisableParallel="$(RestoreDisableParallel)"
      RestoreNoCache="$(RestoreNoCache)"
      RestoreIgnoreFailedSources="$(RestoreIgnoreFailedSources)"
      RestoreRecursive="$(RestoreRecursive)"
      RestoreForce="$(RestoreForce)"
      HideWarningsAndErrors="$(HideWarningsAndErrors)"
      Interactive="$(NuGetInteractive)"
      RestoreForceEvaluate="$(RestoreForceEvaluate)"/>
  </Target>
```

### Inline task

https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-inline-tasks?view=vs-2019

```xml
<UsingTask TaskName="TokenReplace" TaskFactory="CodeTaskFactory" AssemblyFile="$(MSBuildToolsPath)\Microsoft.Build.Tasks.Core.dll">
    <ParameterGroup>
      <Path ParameterType="System.String" Required="true" />
      <Token ParameterType="System.String" Required="true" />
      <Replacement ParameterType="System.String" Required="true" />
    </ParameterGroup>
    <Task>
      <Code Type="Fragment" Language="cs"><![CDATA[
string content = File.ReadAllText(Path);
content = content.Replace(Token, Replacement);
File.WriteAllText(Path, content);

]]></Code>
    </Task>
  </UsingTask>

  <Target Name='Demo' >
    <TokenReplace Path="C:\Project\Target.config" Token="$MyToken$" Replacement="MyValue"/>
  </Target>
```


## Path's tasks

Convert path to absolute path
```xml
<ConvertToAbsolutePath Paths="$(RestoreOutputPath)">
    <Output TaskParameter="AbsolutePaths" PropertyName="RestoreOutputAbsolutePath" />
</ConvertToAbsolutePath>
```

