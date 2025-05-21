'**********************************************************************

'Description:   Duplicate a sysprin

'

'               Ask for a new SYS/PRIN number

'                    If it exists, an update is performed

'               Save the new SYS/PRIN INFO

'**********************************************************************

Private Sub SysPrinDupe(iIncrement As Integer)

Dim sSQL                    As String

Dim sOriginal               As String

Dim lRowsAffected           As Long

Dim iRangeInd               As Integer

Dim lSysPrinRangeEnd        As Long

Dim lTempSysPrin            As Long

Dim iIncrementCnt           As Integer

    If frmEnterSysPrin.optUp1 = True Then

        iIncrementCnt = 1

    End If

    If frmEnterSysPrin.optUp10 = True Then

        iIncrementCnt = 10

    End If

    If frmEnterSysPrin.optUp100 = True Then

        iIncrementCnt = 100

    End If

    If frmEnterSysPrin.optUp1000 = True Then

        iIncrementCnt = 1000

    End If

    'Copy the current SYS/PRIN

    sOriginal = moBankInfo.SysPrin

    'Ask for SYS/PRIN

    'frmEnterSysPrin.Show vbModal

    iRangeInd = frmEnterSysPrin.chkRangeInd.value
 
    'did we get anything back as the number?

    If Me.Tag <> "CANCEL" Then

        If Me.Tag <> "" Then

'            *** GREG This is where we add code for sysprin range.

            If iRangeInd = 1 Then

                lSysPrinRangeEnd = frmEnterSysPrin.txtSysPrinEndRange

                lTempSysPrin = Me.Tag

                 lTempSysPrin = lTempSysPrin + iIncrement

                Do While lTempSysPrin <= lSysPrinRangeEnd

                  moBankInfo.NewSysPrin = lTempSysPrin

                    'Ask to overwrite if the SYS/PRIN exists

                    If moBankInfo.SysPrinExists(moBankInfo.NewSysPrin) Then ' K0I0001

                        If MsgBox("SysPrin: " & moBankInfo.NewSysPrin & vbCrLf & vbCrLf & _

                            "This SYS/PRIN already exists Overwrite?", _

                             vbYesNo + vbCritical) = 1 Then

                            'Save the new sysprin

                            If moBankInfo.SaveNewSysPrin = False Then

                                MsgBox "Failed to duplicate Sys/Prin.", vbOKOnly + vbInformation, "Failed save Sys/Prin"

                                Exit Sub

                            End If

                            ' duplicate the invalid_deliv areas

                            sSQL = "insert into invalid_deliv_areas" _
& " Select '" & moBankInfo.SysPrin & "',area  from" _
& " invalid_deliv_areas where sys_prin = '" & sOriginal & "'"

                            If mSqlADO.ProcessActionQuery(sSQL, lRowsAffected) = False Then ' K0I0001

                                 gLogFile.WriteLog "frmClientSysPrin:SysPrinDupe SQL Error: " _
& vbCrLf & sSQL & vbCrLf & mSqlADO.Error                 ' K0I0001

                                MsgBox "frmClientSysPrin:SysPrinDupe SQL Error - Contact Supervisor"

                                Exit Sub

                            End If

                            lTempSysPrin = lTempSysPrin + iIncrementCnt

                        Else

                            lTempSysPrin = lTempSysPrin + iIncrementCnt

                        End If

                    Else

                        'Save the new sysprin

                        If moBankInfo.SaveNewSysPrin = False Then

                            MsgBox "Failed to duplicate Sys/Prin.", vbOKOnly + vbInformation, "Failed save Sys/Prin"

                            Exit Sub

                        End If

                        ' duplicate the invalid_deliv areas

                        sSQL = "insert into invalid_deliv_areas" _
& " Select '" & moBankInfo.SysPrin & "',area  from" _
& " invalid_deliv_areas where sys_prin = '" & sOriginal & "'"

                        If mSqlADO.ProcessActionQuery(sSQL, lRowsAffected) = False Then ' K0I0001

                             gLogFile.WriteLog "frmClientSysPrin:SysPrinDupe SQL Error: " _
& vbCrLf & sSQL & vbCrLf & mSqlADO.Error                 ' K0I0001

                            MsgBox "frmClientSysPrin:SysPrinDupe SQL Error - Contact Supervisor"

                            Exit Sub

                        End If

                        lTempSysPrin = lTempSysPrin + iIncrementCnt

                    End If

                Loop

            Else

            '*************************************

                moBankInfo.NewSysPrin = Me.Tag
 
                'Ask to overwrite if the SYS/PRIN exists

                If moBankInfo.SysPrinExists(moBankInfo.NewSysPrin) Then ' K0I0001

                    If MsgBox("SysPrin: " & moBankInfo.NewSysPrin & vbCrLf & vbCrLf & _

                        "This SYS/PRIN already exists Overwrite?", _

                        vbYesNo + vbCritical) = 2 Then

                        Exit Sub

                    End If

                End If

                'Save the new sysprin

                If moBankInfo.SaveNewSysPrin = False Then

                    MsgBox "Failed to duplicate Sys/Prin.", vbOKOnly + vbInformation, "Failed save Sys/Prin"

                    Exit Sub

                End If

                ' duplicate the invalid_deliv areas

                 sSQL = "insert into invalid_deliv_areas" _
& " Select '" & moBankInfo.SysPrin & "',area  from" _
& " invalid_deliv_areas where sys_prin = '" & sOriginal & "'"

                If mSqlADO.ProcessActionQuery(sSQL, lRowsAffected) = False Then ' K0I0001

                    gLogFile.WriteLog "frmClientSysPrin:SysPrinDupe SQL Error: " _
& vbCrLf & sSQL & vbCrLf & mSqlADO.Error                 ' K0I0001

                    MsgBox "frmClientSysPrin:SysPrinDupe SQL Error - Contact Supervisor"

                    Exit Sub

                End If

            End If

        End If

    End If
 
    'restore the original SYS/PRIN

     moBankInfo.SysPrin = sOriginal

     Call FindSysPrin(sOriginal)
 
End Sub
 
'**********************************************************************

'Description:   This function Updates/ a sysprin record

'

'Parameters:    sSysPrin - Sys/Prin of the account being evaluated.

'**********************************************************************

Public Function SaveSysPrin() As Boolean

Dim iCount          As Integer

Dim sSQL            As String

Dim lRowsAffected   As Long

Dim bExists         As Boolean
 
    On Error GoTo SaveSysPrinError

    mserror = ""

    SaveSysPrin = False

    If DatabaseConnectionIsMissing Then

        On Error GoTo 0

        Exit Function

    End If

    bExists = SysPrinExists(msSysPrin)

    If Len(mserror) > 0 Then

        On Error GoTo 0

        Exit Function

    End If

    If bExists Then                              'Update sysprin

        sSQL = "Update Sys_prins Set " _
& "Client  = '" & msClient _
& "', cust_type = '" & msCustType _
& "', Undeliverable = '" & msUnable _
& "', stat_a = '" & msStatusA _
& "', stat_b = '" & msStatusB _
& "', stat_c = '" & msStatusC _
& "', stat_d = '" & msStatusD _
& "', stat_e = '" & msStatusE _
& "', stat_f = '" & msStatusF _
& "', stat_i = '" & msStatusI _
& "', stat_l = '" & msStatusL _
& "', stat_o = '" & msStatusO _
& "', stat_u = '" & msStatusU _
& "', stat_x = '" & msStatusX _
& "', stat_z = '" & msStatusZ

        sSQL = sSQL & "', po_box = '" & msPOBox _
& "', non_us = '" & msNonUS _
& "', forwarding_addr = '" & msForwarding _
& "', addr_flag = '" & msAddrFlag _
& "', temp_away = '" & msTempAway _
& "', RPS = '" & msRPS _
& "', Session = '" & msSession _
& "', bad_state = '" & msBadState _
& "', ret_stat ='" & msRetStat _
& "', des_stat ='" & msDesStat _
& "', a_stat_rch = '" & msAStatRch _
& "', nm_13 = '" & msNM13 _
& "', temp_away_atts = " & Val(msTempAwayAtts) _
& ", report_method = " & Val(msReportMethod)

        If mbActive Then

            sSQL = sSQL & ", Active = 1, "

        Else

            sSQL = sSQL & ", Active = 0, "

        End If

        sSQL = sSQL & " Hold_days = " & CStr(mlHoldDays) _
& ", special =    '" & msSpecial _
& "', pin =     '" & msPin _
& "', Notes  =  '" & ConvertQuotes(msNotes) _
& "', Entity_cd  = '" & Trim(str(mudtEntity_cd)) _
& "' WHERE   sys_prin = '" & msSysPrin & "' "
 
 
        If moDB.ProcessActionQuery(sSQL, lRowsAffected) = False Then

            mserror = "cBank:SaveSysPrin SQL Error: " & vbCrLf _
& sSQL & vbCrLf & moDB.Error

            gLogFile.WriteLog mserror

            On Error GoTo 0

            Exit Function

        End If

        If lRowsAffected = -1 Then

            iCount = 1

        Else

            iCount = lRowsAffected

        End If

        If iCount > 0 Then

            SaveSysPrin = True

        End If

     Else

        sSQL = "insert into Sys_prins" _
& " (Sys_Prin, Client, cust_type, Undeliverable, stat_a, stat_b, stat_c," _
& " stat_d, stat_e, stat_f, stat_i, stat_l, stat_o, stat_u, stat_x," _
& " stat_z, po_box, non_us, addr_flag, temp_away, RPS, Session, bad_state," _
& " ret_stat, des_stat, a_stat_rch, nm_13, temp_away_atts, report_method," _
& " Active, Hold_days, Special, pin, Notes, Entity_cd, forwarding_addr) VALUES ("

        sSQL = sSQL & " '" & msSysPrin _
& "', '" & msClient _
& "', '" & msCustType _
& "', '" & msUnable _
& "', '" & msStatusA _
& "', '" & msStatusB _
& "', '" & msStatusC _
& "', '" & msStatusD _
& "', '" & msStatusE _
& "', '" & msStatusF _
& "', '" & msStatusI _
& "', '" & msStatusL _
& "', '" & msStatusO _
& "', '" & msStatusU

        sSQL = sSQL & "', '" & msStatusX _
& "', '" & msStatusZ _
& "', '" & msPOBox _
& "', '" & msNonUS _
& "', '" & msAddrFlag _
& "', '" & msTempAway _
& "', '" & msRPS _
& "', '" & msSession _
& "', '" & msBadState _
& "', '" & msRetStat _
& "', '" & msDesStat _
& "', '" & msAStatRch _
& "', '" & msNM13 _
& "', " & Val(msTempAwayAtts) _
& ", " & Val(msReportMethod)

        If mbActive Then

            sSQL = sSQL & ", 1, "

        Else

            sSQL = sSQL & ", 0, "

        End If

        sSQL = sSQL & mlHoldDays _
& ", '" & msSpecial _
& "', '" & msPin _
& "', '" & ConvertQuotes(msNotes) _
& "', '" & Trim(str(mudtEntity_cd)) _
& "', '" & msForwarding & "')"
 
        If moDB.ProcessActionQuery(sSQL, lRowsAffected) = False Then

            mserror = "cBank:SaveSysPrin SQL Error" _
& vbCrLf & sSQL & vbCrLf & moDB.Error

            gLogFile.WriteLog mserror

            On Error GoTo 0

            Exit Function

        End If

        If lRowsAffected = -1 Then

            iCount = 1

        Else

            iCount = lRowsAffected

        End If

        If iCount > 0 Then

            SaveSysPrin = True

        End If

    End If

    On Error GoTo 0

    Exit Function
 
SaveSysPrinError:

    mserror = "cBank:SaveSysPrin Error: " & Err.Number & Err.Description

    gLogFile.WriteLog mserror

    On Error GoTo 0
 
End Function
 
cBank.vb
 
can I call?
 
