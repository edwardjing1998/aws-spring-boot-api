Private Sub tabSysPrin_Click(PreviousTab As Integer)
 
    Const sInputFile As String = "I"

    Const sOutputFile As String = "O"
 
    Select Case tabSysPrin.Tab

        Case TAB_SYSPRIN_FILE_SENT_TO

            FillVendorlst sInputFile

        Case TAB_SYSPRIN_FILE_RECEIVED_FROM

            FillVendorlst sOutputFile

    End Select

 
End Sub
 
'**********************************************************************

'Description:   FillVendorlst

'

'               Fetch the Vendor lst box

'**********************************************************************

Private Sub FillVendorlst(ByVal sFile_Type As String)
 
Dim sFile                   As String

Dim rc                      As ADODB.RecordSet                      ' K0I0001

Dim sSQL                    As String
 
    sFile = sFile_Type
 
    'Clear the old list

    While lstVendoravail.listcount > 0

        lstVendoravail.RemoveItem (0)

    Wend

    While lstVendor.listcount > 0

        lstVendor.RemoveItem (0)

    Wend
 
    sSQL = "SELECT Vend_id, Vend_nm, Vend_rcvr_cd, Vend_fsrv_nm, Vend_fsrv_ip" _
& " FROM VENDOR WHERE VEND_ACTV_CD = 1 and Vend_file_IO ='" & Trim$(sFile) & "'"

    If mSqlADO.ExecSQLCommand(sSQL, rc) = False Then                ' K0I0001

        MsgBox "frmClientSysPrin:FillVendorlst SQL Error - Contact Supervisor"

        gLogFile.WriteLog "frmClientSysPrin:FillVendorlst SQL Error: " _
& vbCrLf & sSQL & vbCrLf & mSqlADO.Error                ' K0I0001

        Exit Sub

    End If   
 
    While Not rc.Eof

       lstVendoravail.AddItem (rc!Vend_id & " " & rc!Vend_nm)

       lstVendor.AddItem (rc!Vend_id & " " & rc!Vend_nm)

       rc.MoveNext

    Wend
 
    Set rc = Nothing
 
End Sub
 
Const TAB_SYSPRIN_FILE_SENT_TO = 4

Const TAB_SYSPRIN_FILE_RECEIVED_FROM = 5




vendorReceivedFrom and vendorSentTo 



 
