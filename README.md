







'Description:   cmdSysPrinAll_Click()
'               Change all SYS/PRINS to the same settings as the current
'               SYS/PRIN
'
'               1.  Create list of SYS/PRINS for Client
'               2.  Loop through the list and set the SYS/PRIN
'                   on the class module and call the classe's Save
'                   Update the invalid delivery areas for the sysprin
'               3.  write a message to the log file
'**********************************************************************
Private Sub cmdSysPrinAll_Click()
Dim sOriginal               As String
Dim sClient                 As String
Dim rcSysList               As ADODB.RecordSet                      ' K0I0001
Dim sSQL                    As String
Dim iCount                  As Integer
 
    If moBankInfo.SysPrin = "" Then
        Call MsgBox("Select a SYS/PRIN from the list to copy", _
            vbOKCancel + vbExclamation, "SYS/PRIN Needed")
        Set rcSysList = Nothing
        Exit Sub
    End If
 
    'Do you really want to?
    If MsgBox("Warning This operation will change all " & vbCrLf _
              & "of the SYS/PRIN options for this client", _
              vbCritical + vbOKCancel, _
              "Copy All SYS/PRIN") = vbCancel Then
        Set rcSysList = Nothing
        Exit Sub
    End If
    
    iCount = 0
    sOriginal = moBankInfo.SysPrin
    sClient = moBankInfo.client
    Screen.MousePointer = vbHourglass
    
    sSQL = "select sys_prin from sys_prins " _
        & " where Client = '" & sClient & "' ORDER BY SYS_PRIN "
 
    Call SetPanel("Copy Original SYS/PRIN " & sOriginal, 1)
 
    If mSqlADO.ExecSQLCommand(sSQL, rcSysList) = False Then         ' K0I0001
        MsgBox "Client SysPrins could not be retrieved due to the following error: " _
            & vbCrLf & mSqlADO.Error, vbInformation + vbOKOnly, "Rapid Admin"
        gLogFile.WriteLog "frmClientSysPrin:cmdSysPrinAll_Click SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & mSqlADO.Error                ' K0I0001
        Exit Sub
    End If
 
    Do While Not rcSysList.Eof
        iCount = iCount + 1
        Screen.MousePointer = vbHourglass
        Call SetPanel("Updating...  SYS/PRIN " & rcSysList!sys_prin, 2)
 
         moBankInfo.NewSysPrin = rcSysList!sys_prin
         Call moBankInfo.SaveNewSysPrin                             ' K0I0001
         '  duplicate the invalid_deliv areas
         
         sSQL = "insert into invalid_deliv_areas" _
            & " Select '" & moBankInfo.SysPrin & "',area  from" _
            & " invalid_deliv_areas where sys_prin = '" & sOriginal & "'"
 
        If mSqlADO.ProcessActionQuery(sSQL) = False Then            ' K0I0001
            MsgBox "Failed to update the invalid delivery areas for SysPrin '" _
                & moBankInfo.SysPrin & "'." _
                & vbCrLf & "The following error was received: " _
                & vbCrLf & mSqlADO.Error, vbInformation + vbOKOnly, "Rapid Admin"
            gLogFile.WriteLog "frmClientSysPrin:cmdSysPrinAll_Click SQL Error: " _
                & vbCrLf & sSQL & vbCrLf & mSqlADO.Error            ' K0I0001
            Exit Sub
        End If
 
        DoEvents
        Screen.MousePointer = vbNormal
        rcSysList.MoveNext
 
    Loop
 
    'Restore the original SYS/PRIN
    moBankInfo.SysPrin = sOriginal
 
    'Log it
    Screen.MousePointer = vbNormal
    Set rcSysList = Nothing
    Call ClearPanel
    Call SetPanel(iCount & " SYS/PRINS Updated", 1)
 
End Sub



