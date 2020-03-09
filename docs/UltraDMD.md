# UltraDMD

FlexDMD was created since UltraDMD has a few bugs and it was not updated any more. Therefore, it is designed to easily replace UltraDMD on existing tables.

Note that the script folder of this repository contains scripts which includes the changes below for a few popular tables.

## Converting a table from UltraDMD to FlexDMD

To convert a table to FlexDMD, load the table in VPX, open the script, then find a line containing `Set UltraDMD = CreateObject("UltraDMD.DMDObject")` and replace it with `Set FlexDMD = CreateObject("FlexDMD.FlexDMD")`.

You should share the game name with the render device (This allows virtual DMD to adapt their rendering to the played table with per table layout, contextual frame,...). To do so, simply add `FlexDMD.GameName = cGameName` before `Init`.

UltraDMD does not define if the table is monochrome or color from the script but from its UI. FlexDMD lets this choice to the table designer. So, to enable colors, you have to add `FlexDMD.RenderMode = 2` before `Init`.

Finally, create the UltraDMD's API object by calling `NewUltraDMD()` and call `Init`.

As an example, here is the modification for the [great Diablo III table](https://www.vpforums.org/index.php?app=downloads&showfile=12750) from JPSalas:
```VBScript
Sub DMD_Init
    'Set UltraDMD = CreateObject("UltraDMD.DMDObject")

    Dim FlexDMD
    Set FlexDMD = CreateObject("FlexDMD.FlexDMD")
    If FlexDMD is Nothing Then
        MsgBox "No UltraDMD found.  This table will NOT run without it."
        Exit Sub
    End If
    FlexDMD.GameName = cGameName
    FlexDMD.RenderMode = 2
    Set UltraDMD = FlexDMD.NewUltraDMD()
    UltraDMD.Init
    
    If Not UltraDMD.GetMajorVersion = 1 Then
        MsgBox "Incompatible Version of UltraDMD found."
        Exit Sub
    End If
	
    ...
	
End Sub
```

If the table is missing UltraDMD Uninit call on exit, you should also add it in `table1_Exit` like this:
```VBScript
Sub table1_Exit
    If Not UltraDMD is Nothing Then
        If UltraDMD.IsRendering Then
            UltraDMD.CancelRendering
        End If
        UltraDMD.Uninit
        UltraDMD = NULL
    End If
    ...
End Sub
```

