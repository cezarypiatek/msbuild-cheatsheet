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

