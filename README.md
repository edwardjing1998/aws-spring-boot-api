'*****************************************************************************
'
'           Title:   Rapid Bank class
' Original Author:   DPRC
'       $Workfile:   cBank.cls  $
'        $Archive:   //Csdbuild1/Dev/SCM/Rapid/archives/RapidCommon/cBank.cls-arc  $
'       $Revision:   1.37  $
'           $Date:   Nov 17 2011 11:43:28  $
'         $Author:   dboisen  $
'          Notice:   (c) COPYRIGHT FIRST DATA RESOURCES Inc.
'                    1971 - 2003
'                    All Rights Reserved
' This media contains unpublished, confidential and proprietary
' information of First Data Resources Inc.  No disclosure or use
' of any portion of the contents of these materials may be made
' without the express written consent of First Data Resources Inc.
'*****************************************************************************
'                           MAINTENANCE HISTORY
'
' $Log:   //Csdbuild1/Dev/SCM/Rapid/archives/RapidCommon/cBank.cls-arc  $
'
'    Rev 1.37   Nov 17 2011 11:43:28   dboisen
' Corrections to Move Sysprin process.
'
'    Rev 1.36   Oct 25 2011 12:43:06   dboisen
' Modify insert/update sysprin SQL
'
'    Rev 1.35   May 23 2007 09:14:22   GJGUIDA
' Revised made to add sysprin ranges project C7DB017.
'
'    Rev 1.34   Feb 13 2007 07:27:30   GJGUIDA
' Fix to Labels for Qued for mail
'
'    Rev 1.33   Dec 26 2006 12:20:38   GJGUIDA
' Removal of Amex Hardcode, C6BB007
'
'    Rev 1.32   Dec 26 2006 11:29:52   GJGUIDA
' Amex Hardcode removal, C6BB007
'
'    Rev 1.31   Aug 21 2006 07:55:58   GJGUIDA
' IR Fix for Client update.
'
'    Rev 1.30   Aug 14 2006 08:34:38   GJGUIDA
' Updates for Web reporting.
'
'    Rev 1.29   Mar 10 2006 10:31:12   DDCROSS
' Switch to pure Offline/FDRHost processing
'
'    Rev 1.28   May 19 2005 10:22:30   GJGUIDA
' Revision to naming standards for fax number.
'
'    Rev 1.27   May 18 2005 10:26:36   GJGUIDA
' Changes made for Project C5A0120. (Properties to cBanks.cls, Logging in cCase.cls, Const added to RapiGlobals for searchtype.
'
'    Rev 1.26   Aug 18 2004 07:25:02   gjguida
' Version Correction
'
'    Rev 1.25   Aug 18 2004 07:18:52   gjguida
' Revised new code for adding clients.
'
'    Rev 1.24   Dec 30 2003 17:21:02   ddcross
' Change the client insert statement to explicitly list the columns to insert.
'
'    Rev 1.23   Dec 12 2003 15:51:12   ddcross
' Change the name of the Report_Break column to Report_Break_Flag
'
'    Rev 1.22   Dec 01 2003 15:55:46   ddcross
' Allow clients to break reports so sys/prin end up on separate pages
'
'    Rev 1.21   Sep 24 2003 16:01:54   ddcross
' B3D0027 & B3E0063, Code Rework resulting from the code QI
'
'    Rev 1.20   Sep 18 2003 08:54:36   ddcross
' B3E0063 - Allow processing FDR accounts not on host
' B3D0027 - Enhance Rapid Labels
'
'    Rev 1.19   Mar 11 2003 16:13:42   ddcross
' Remove Chronicle Memo Filter ID of Rapid, and add the ability to retrieve up to 1000 Chronicle memos.
' Enhance ability to detect loss of cSqlADO connections
'
'    Rev 1.18   Feb 28 2003 11:05:42   ddcross
' K2L0004 - Fix Chronicle Memo
'
'    Rev 1.17   Dec 23 2002 14:47:58   ddcross
' Fourth set of fixes for Cap System Testing
'
'    Rev 1.16   Dec 20 2002 17:23:00   ddcross
' Fix the third set of issues from CAP system testing
'
'    Rev 1.15   Oct 04 2002 10:02:30   jbwood
' Updated revision to current
'
'    Rev 1.12.1.1   Aug 12 2002 14:27:30   dlegger
' Standardize error handling, Roll up from 1.8: Fix PI_NonMon processing
' IR 06361075 fixes, Simplified form, Removed timers.
' Modified Chronicle Memo Processing
' Fixed add new sys/prin bug.
' Roll up 2.0 changes to 2.5
'
'    Rev 1.12.1.0   Jul 08 2002 15:12:58   dlegger
' Modified Chronicle Memo Processing.
'
'    Rev 1.12   Jun 25 2002 12:39:10   dlegger
' Fixed bug in GetPCFInfo (cCardholder)
' Added GetAnySysPrin to CBank and Optional parm to GetSysPrin.
'
'    Rev 1.11   Jun 24 2002 15:22:00   dlegger
' Adding Chronicle memo processing/viewing
'
'    Rev 1.10   May 16 2002 15:02:16   ddcross
' Enhance exception handling to prevent creating duplicate table entries due to a failed check to see if an entry already exists.
'
'    Rev 1.9   Apr 17 2002 09:49:40   ddcross
' Unit test fixes
' Remove Commented Code
' Add globals to support the e-mail class
'
'    Rev 1.8   Mar 20 2002 11:32:58   jbwood
' Added error checking in GetPrefix for Admin
' Changed Hold # days from string to long
'
'    Rev 1.7   Mar 18 2002 12:19:40   ddcross
' CAP Changes to common modules to support Rapid Robot
'
'    Rev 1.6   Mar 11 2002 13:38:56   ddcross
' Change cBank, cCase and cCardHolder classes to use a property to set an internal reference to the cSqlAdo connection instance.
'
'    Rev 1.5   Mar 02 2002 10:12:32   jbwood
' Added initialize of Error property in function/method
'
'    Rev 1.4   Mar 01 2002 13:19:10   jbwood
' Major CAP Restructuring
'
'    Rev 1.3   Feb 06 2002 20:19:32   jbwood
' Removed "Contact Supervisor" from error messages
'
'V1.1.1     DLE     Added to GetSysPrin_SQL to retrieve rules for PIN
'                       and ATM/Cash Card Processing
'V1.0.1     DLE     Added private pin and atm variables and Get and Let functions
'V1.0.0     DLE     Added PIN and ATM_CASH_CARD to GetSysPrin_SQL
'---------------------------------------------------------
Option Explicit

'Data Attributes
Private msSysPrin           As String
Private msClient            As String

Private msName              As String
Private msAddr              As String
Private msCity              As String
Private msState             As String
Private msZip               As String
Private msContact           As String
Private msPhone             As String
Private msFaxNumber         As String
Private msBillingSP         As String
Private mbClientActive      As Boolean
Private mbExcludeFromReport As Boolean
Private mbPositiveReports   As Boolean
Private mbSubClient         As Boolean
Private msSubClientXref     As String
Private miReportBreak       As Integer
Private miSearchType        As Integer
Private mbAmexIssued        As Boolean

Private msCustType          As String
Private msUnable            As String   'Unable to deliver
Private msStatusA           As String
Private msStatusB           As String
Private msStatusC           As String
Private msStatusD           As String
Private msStatusE           As String
Private msStatusF           As String
Private msStatusI           As String
Private msStatusL           As String
Private msStatusO           As String
Private msStatusU           As String
Private msStatusX           As String
Private msStatusZ           As String
Private msPOBox             As String
Private msNonUS             As String
Private msForwarding        As String
Private msAddrFlag          As String
Private msTempAway          As String
Private msRPS               As String
Private msSession           As String
Private msBadState          As String
Private msRetStat           As String
Private msDesStat           As String
Private msAStatRch          As String
Private msNM13              As String
Private msTempAwayAtts      As String
Private msReportMethod      As String
Private mbActive            As Boolean
Private mlHoldDays          As Long   'koi0001f
Private msSpecial           As String 'koi0001
Private msPin               As String
Private msNotes             As String
Private mudtEntity_cd       As ENTITY_IN

Private msATMCash           As String

Private msNewSysPrin        As String      ' Temporary Sysprin for new or dupe
Private mserror             As String
Private mbSuccess           As Boolean
Private moDB                As CSqlADO


Public Property Set DB(oSql As CSqlADO)
    If moDB Is Nothing = False Then
        Set moDB = Nothing
    End If
    
    Set moDB = oSql
End Property

Public Property Get client() As String
    client = msClient
End Property

Public Property Let client(sNewValue As String)
    If msClient <> Trim$(sNewValue) Then
        Me.ClientCleanUp
        msClient = Trim$(sNewValue)
        If msClient <> "" Then
            mbSuccess = GetClient()
        End If
    End If

End Property

Public Property Get SysPrin() As String
    SysPrin = msSysPrin
End Property

Public Property Let SysPrin(sNewValue As String)

    If msSysPrin <> sNewValue Then
        Me.SysPrinCleanUp
        msSysPrin = sNewValue
        If Len(msSysPrin) > 0 Then
            mbSuccess = GetSysPrin()
        End If
    End If

End Property

Public Property Get Name() As String
    Name = Trim$(msName)
End Property

Public Property Let Name(sNewValue As String)
    msName = Trim$(sNewValue)
End Property

Public Property Get Addr() As String
    Addr = Trim$(msAddr)
End Property

Public Property Let Addr(sNewValue As String)
    msAddr = Trim$(sNewValue)
End Property

Public Property Get City() As String
    City = Trim$(msCity)
End Property

Public Property Let City(sNewValue As String)
    msCity = Trim$(sNewValue)
End Property

Public Property Get State() As String
    State = Trim$(msState)
End Property

Public Property Let State(sNewValue As String)
    msState = Trim$(sNewValue)
End Property

Public Property Get Zip() As String
    Zip = Trim$(msZip)
End Property

Public Property Let Zip(sNewValue As String)
    msZip = Trim$(sNewValue)
End Property

Public Property Get Contact() As String
    Contact = Trim$(msContact)
End Property

Public Property Let Contact(sNewValue As String)
    msContact = Trim$(sNewValue)
End Property

Public Property Get Phone() As String
    Phone = Trim$(msPhone)
End Property

Public Property Let Phone(sNewValue As String)
    msPhone = Trim$(sNewValue)
End Property

Public Property Get FaxNumber() As String
    FaxNumber = Trim$(msFaxNumber)
End Property

Public Property Let FaxNumber(sNewValue As String)
    msFaxNumber = Trim$(sNewValue)
End Property

Public Property Get BillingSP() As String
    BillingSP = msBillingSP
End Property

Public Property Let BillingSP(ByVal sNewValue As String)
    msBillingSP = sNewValue
End Property

Public Property Get ClientActive() As Boolean
    ClientActive = mbClientActive
End Property

Public Property Let ClientActive(ByVal bNewValue As Boolean)
    mbClientActive = bNewValue
End Property

Public Property Get ExcludeFromReport() As Boolean
    ExcludeFromReport = mbExcludeFromReport
End Property

Public Property Let ExcludeFromReport(ByVal bNewValue As Boolean)
    mbExcludeFromReport = bNewValue
End Property

Public Property Get PositiveReports() As Boolean
    PositiveReports = mbPositiveReports
End Property

Public Property Let PositiveReports(ByVal bNewValue As Boolean)
    mbPositiveReports = bNewValue
End Property

Public Property Get AmexIssued() As Boolean
    AmexIssued = mbAmexIssued
End Property

Public Property Let AmexIssued(ByVal bNewValue As Boolean)
    mbAmexIssued = bNewValue
End Property

Public Property Get SubClient() As Boolean
    SubClient = mbSubClient
End Property

Public Property Let SubClient(ByVal bNewValue As Boolean)
    mbSubClient = bNewValue
End Property

Public Property Get SubClientXref() As String
    SubClientXref = msSubClientXref
End Property

Public Property Let SubClientXref(ByVal sNewValue As String)
    msSubClientXref = sNewValue
End Property

Public Property Get ReportBreak() As Integer
    ReportBreak = miReportBreak
End Property

Public Property Let ReportBreak(ByVal iNewValue As Integer)
    miReportBreak = iNewValue
End Property

Public Property Get SearchType() As Integer
    SearchType = miSearchType
End Property

Public Property Let SearchType(ByVal iNewValue As Integer)
    miSearchType = iNewValue
End Property

Public Property Get CustType() As String
    CustType = Trim$(msCustType)
End Property

Public Property Let CustType(sNewValue As String)
    msCustType = Trim$(sNewValue)
End Property

Public Property Get Undeliverable() As String
    Undeliverable = Trim$(msUnable)
End Property


Public Property Let Undeliverable(sNewValue As String)
    msUnable = Trim$(sNewValue)
End Property

Public Property Get StatusA() As String
    StatusA = Trim$(msStatusA)
End Property


Public Property Let StatusA(sNewValue As String)
    msStatusA = Trim$(sNewValue)
End Property


Public Property Get StatusB() As String
    StatusB = Trim$(msStatusB)
End Property


Public Property Let StatusB(sNewValue As String)
    msStatusB = Trim$(sNewValue)
End Property


Public Property Get StatusC() As String
    StatusC = Trim$(msStatusC)
End Property


Public Property Let StatusC(sNewValue As String)
    msStatusC = Trim$(sNewValue)
End Property


Public Property Get StatusD() As String
    StatusD = Trim$(msStatusD)
End Property


Public Property Let StatusD(sNewValue As String)
    msStatusD = Trim$(sNewValue)
End Property


Public Property Get StatusE() As String
    StatusE = Trim$(msStatusE)
End Property


Public Property Let StatusE(sNewValue As String)
    msStatusE = Trim$(sNewValue)
End Property


Public Property Get StatusF() As String
    StatusF = Trim$(msStatusF)
End Property


Public Property Let StatusF(sNewValue As String)
    msStatusF = Trim$(sNewValue)
End Property


Public Property Get StatusI() As String
    StatusI = Trim$(msStatusI)
End Property


Public Property Let StatusI(sNewValue As String)
    msStatusI = Trim$(sNewValue)
End Property


Public Property Get StatusL() As String
    StatusL = Trim$(msStatusL)
End Property


Public Property Let StatusL(sNewValue As String)
    msStatusL = Trim$(sNewValue)
End Property


Public Property Get StatusO() As String
    StatusO = Trim$(msStatusO)
End Property


Public Property Let StatusO(sNewValue As String)
    msStatusO = Trim$(sNewValue)
End Property


Public Property Get StatusU() As String
    StatusU = Trim$(msStatusU)
End Property


Public Property Let StatusU(sNewValue As String)
    msStatusU = Trim$(sNewValue)
End Property


Public Property Get StatusX() As String
    StatusX = Trim$(msStatusX)
End Property


Public Property Let StatusX(sNewValue As String)
    msStatusX = Trim$(sNewValue)
End Property


Public Property Get StatusZ() As String
    StatusZ = Trim$(msStatusZ)
End Property


Public Property Let StatusZ(sNewValue As String)
    msStatusZ = Trim$(sNewValue)
End Property


Public Property Get POBox() As String
    POBox = Trim$(msPOBox)
End Property


Public Property Let POBox(sNewValue As String)
    msPOBox = Trim$(sNewValue)
End Property


Public Property Get NonUS() As String
    NonUS = Trim$(msNonUS)
End Property


Public Property Let NonUS(sNewValue As String)
    msNonUS = Trim$(sNewValue)
End Property

Public Property Get Forwarding() As String
    Forwarding = Trim$(msForwarding)
End Property


Public Property Let Forwarding(sNewValue As String)
    msForwarding = Trim$(sNewValue)
End Property

Public Property Get AddrFlag() As String
    AddrFlag = Trim$(msAddrFlag)
End Property


Public Property Let AddrFlag(sNewValue As String)
    msAddrFlag = Trim$(sNewValue)
End Property


Public Property Get TempAway() As String
    TempAway = Trim$(msTempAway)
End Property


Public Property Let TempAway(sNewValue As String)
    msTempAway = Trim$(sNewValue)
End Property


Public Property Get RPS() As String
    RPS = Trim$(msRPS)
End Property


Public Property Let RPS(sNewValue As String)
    msRPS = Trim$(sNewValue)
End Property


Public Property Get Session() As String
    Session = Trim$(msSession)
End Property


Public Property Let Session(sNewValue As String)
    msSession = Trim$(sNewValue)
End Property


Public Property Get BadState() As String
    BadState = Trim$(msBadState)
End Property


Public Property Let BadState(sNewValue As String)
    msBadState = Trim$(sNewValue)
End Property


Public Property Get RetStat() As String
    RetStat = Trim$(msRetStat)
End Property


Public Property Let RetStat(sNewValue As String)
    msRetStat = Trim$(sNewValue)
End Property


Public Property Get DesStat() As String
    DesStat = Trim$(msDesStat)
End Property


Public Property Let DesStat(sNewValue As String)
    msDesStat = Trim$(sNewValue)
End Property


Public Property Get A_Stat_Rch() As String
    A_Stat_Rch = Trim$(msAStatRch)
End Property


Public Property Let A_Stat_Rch(sNewValue As String)
    msAStatRch = Trim$(sNewValue)
End Property


Public Property Get NM13() As String
    NM13 = Trim$(msNM13)
End Property


Public Property Let NM13(sNewValue As String)
    msNM13 = Trim$(sNewValue)
End Property


Public Property Get TempAwayAtts() As String
    TempAwayAtts = Trim$(msTempAwayAtts)
End Property


Public Property Let TempAwayAtts(sNewValue As String)
    msTempAwayAtts = Trim$(sNewValue)
End Property


Public Property Get ReportMethod() As String
    ReportMethod = Trim$(msReportMethod)
End Property


Public Property Let ReportMethod(sNewValue As String)
    msReportMethod = Trim$(sNewValue)
End Property


Public Property Get Active() As Boolean
    Active = mbActive
End Property


Public Property Let Active(bNewValue As Boolean)
    mbActive = bNewValue
End Property


Public Property Get Holddays() As Long 'koi0001
    Holddays = mlHoldDays
End Property


Public Property Let Holddays(lNewValue As Long)
    mlHoldDays = Trim$(lNewValue)
End Property


Public Property Get Special() As String 'koi0001
    Special = msSpecial
End Property


Public Property Let Special(sNewValue As String)
    msSpecial = Trim$(sNewValue)
End Property


Public Property Get Pin() As String
    Pin = msPin
End Property


Public Property Let Pin(sNewValue As String)
    msPin = Trim$(sNewValue)
End Property


Public Property Get ATMCash() As String
    ATMCash = msATMCash
End Property


Public Property Let ATMCash(sNewValue As String)
    msATMCash = Trim$(sNewValue)
End Property


Public Property Get Notes() As String
    Notes = msNotes
End Property


Public Property Let Notes(ByVal sNewValue As String)
    msNotes = Trim$(sNewValue)
End Property


Public Property Get Entity_cd() As ENTITY_IN
    Entity_cd = mudtEntity_cd
End Property


Public Property Let Entity_cd(ByVal udtNewValue As ENTITY_IN)
    mudtEntity_cd = udtNewValue
End Property


Public Property Get NewSysPrin() As String
    NewSysPrin = msNewSysPrin
End Property


Public Property Let NewSysPrin(ByVal sNewValue As String)
    msNewSysPrin = Trim$(sNewValue)
End Property


Public Property Get Error() As String
    Error = mserror
End Property


Public Property Get Success() As Boolean
    Success = mbSuccess
End Property


Private Sub Class_Initialize()
    CleanUp
End Sub

Private Sub Class_Terminate()

    Me.CleanUp
    Set moDB = Nothing
    
End Sub


Public Sub CleanUp()
    
    ClientCleanUp
    SysPrinCleanUp

End Sub


Public Sub ClientCleanUp()
    
    msClient = ""
    msName = ""
    msAddr = ""
    msCity = ""
    msState = ""
    msZip = ""
    msContact = ""
    msPhone = ""
    msFaxNumber = ""
    msBillingSP = ""
    msSubClientXref = ""
    mbClientActive = False
    mbExcludeFromReport = False
    mbPositiveReports = False
    mbAmexIssued = False
    mbSubClient = False
    miReportBreak = BREAK_NONE
    miSearchType = SEARCH_STANDARD

End Sub


Public Sub SysPrinCleanUp()
    
    msSysPrin = ""
    msCustType = ""
    msUnable = ""
    msStatusA = ""
    msStatusB = ""
    msStatusC = ""
    msStatusD = ""
    msStatusE = ""
    msStatusF = ""
    msStatusI = ""
    msStatusL = ""
    msStatusO = ""
    msStatusU = ""
    msStatusX = ""
    msStatusZ = ""
    msPOBox = ""
    msNonUS = ""
    msForwarding = ""
    msAddrFlag = ""
    msTempAway = ""
    msRPS = ""
    msSession = ""
    msBadState = ""
    msRetStat = ""
    msDesStat = ""
    msAStatRch = ""
    msNM13 = ""
    msTempAwayAtts = ""
    msReportMethod = ""
    mbActive = False
    mlHoldDays = 0
    msSpecial = ""
    msPin = ""
    msATMCash = ""
    msNotes = ""
    mudtEntity_cd = SINGLE_ENTITY
    miSearchType = 0
    mbAmexIssued = False
    msNewSysPrin = ""
    mserror = ""
    mbSuccess = False

End Sub


Public Function DeletePrefix(sPrefix As String) As Boolean
Dim sSQL As String

    On Error GoTo DeletePrefixError

    DeletePrefix = False
    mserror = ""

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "DELETE FROM SYS_PRINS_PREFIX WHERE BILLING_SP = '" _
            & msBillingSP & "' AND PREFIX = '" & sPrefix & "'"

    If moDB.ProcessActionQuery(sSQL) = False Then                    ' K0I0001
        mserror = "cBank:DeletePrefix SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        On Error GoTo 0
        Exit Function
    End If

    DeletePrefix = True
    On Error GoTo 0
    Exit Function

DeletePrefixError:
    mserror = "cBank:PrefixExist - " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    On Error GoTo 0

End Function


Public Function PrefixExist(sPrefix As String) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet                                           ' K0I0001

    On Error GoTo PrefixExistError

    PrefixExist = False
    mserror = ""

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "select count(*) as TheCount from sys_prins_prefix" _
            & " where billing_sp = '" & msBillingSP _
                & "' and prefix = '" & sPrefix & "'"

    If moDB.ExecSQLCommand(sSQL, rc) = False Then                    ' K0I0001
        PrefixExist = False
        mserror = "cBank:PrefixExist SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error
        gLogFile.WriteLog mserror                                   ' K0I0001
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If

    If rc.Eof Then
        rc.Close
        Set rc = Nothing
        Exit Function
    Else
        If rc(0) > 0 Then
            PrefixExist = True
        End If
    End If
    
    rc.Close
    Set rc = Nothing
    On Error GoTo 0
    Exit Function

PrefixExistError:
    mserror = "cBank:PrefixExist - " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


Public Function InsertPrefix(sPrefix As String, _
                            sRule As String) As Boolean             ' K0I0001
Dim sSQL As String
    
    On Error GoTo InsertPrefixError

    InsertPrefix = False
    mserror = ""

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "INSERT INTO SYS_PRINS_PREFIX (BILLING_SP, PREFIX, ATM_CASH_RULE) Values ('" _
        & msBillingSP & "', '" & sPrefix & "', '" & sRule & "')"

    If moDB.ProcessActionQuery(sSQL) = False Then                            ' K0I0001
        mserror = "cBank:InsertPrefix SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        On Error GoTo 0
        Exit Function
    End If

    InsertPrefix = True
    On Error GoTo 0
    Exit Function

InsertPrefixError:
    mserror = "cBank:PrefixExist - " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    On Error GoTo 0
    
End Function


Public Function GetPrefix(sPrefix As String, sSysPrin As String) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet
Dim sClient As String

    On Error GoTo GetPrefixError

    GetPrefix = False
    mserror = ""
    sSysPrin = ""

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "SET ROWCOUNT 1 " _
        & "SELECT DISTINCT cl.client, cl.name, cl.billing_sp, s.atm_cash_rule " _
        & "FROM clients cl, sys_prins_prefix s " _
        & "WHERE s.prefix = '" & sPrefix & "' AND s.billing_sp = cl.billing_sp " _
        & "SET ROWCOUNT 0"

    If moDB.ExecSQLCommand(sSQL, rc) = False Then
        mserror = "cBank:GetPrefix SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If

    If rc.Eof Then
        rc.Close
        Set rc = Nothing
        Exit Function
    Else
        'Populate class variables
        msATMCash = Trim$(rc!atm_cash_rule & "")
        msBillingSP = Trim$(rc!billing_sp & "")
        sClient = Trim$(rc!client & "")

        'Get 1st matching Sys/Prin where client = cl.client
        
        sSQL = "SET ROWCOUNT 1 " _
            & "SELECT sys_prin FROM sys_prins " _
            & "WHERE client = '" & sClient & "' " _
            & "SET ROWCOUNT 0"

        If moDB.ExecSQLCommand(sSQL, rc) Then
            If rc.Eof = False Then
                sSysPrin = Trim$(rc!sys_prin & "")
            Else
                mserror = "cBank:GetPrefix Client has no SysPrin entries"
                gLogFile.WriteLog mserror
                Set rc = Nothing
                On Error GoTo 0
                Exit Function
            End If
        Else
            mserror = "cBank:GetPrefix SQL Error: " _
                & vbCrLf & sSQL & vbCrLf & moDB.Error
            gLogFile.WriteLog mserror
        End If
    End If

    GetPrefix = True
    Set rc = Nothing
    On Error GoTo 0
    Exit Function

GetPrefixError:
    mserror = "cBank:GetPrefix Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


Public Function TempGetPrefix(rs As ADODB.RecordSet) As Boolean
Dim sSQL As String

    On Error GoTo TempGetPrefixError

    TempGetPrefix = False
    mserror = ""

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "SELECT PREFIX FROM SYS_PRINS_PREFIX" _
            & " WHERE BILLING_SP = '" & msBillingSP & "' ORDER BY PREFIX"

    If moDB.ExecSQLCommand(sSQL, rs) = False Then                    ' K0I0001
        mserror = "cBank:GetPrefix SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        On Error GoTo 0
        Exit Function
    End If

    TempGetPrefix = True
    On Error GoTo 0
    Exit Function

TempGetPrefixError:
    mserror = "cBank:TempGetPrefix Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    On Error GoTo 0

End Function
 
 
Public Function SaveATMRule(sRule As String, _
                            sPrefix As String) As Boolean           ' K0I0001
Dim sSQL As String

    On Error GoTo SaveATMRuleError
    
    mserror = ""
    SaveATMRule = False
    
    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "UPDATE SYS_PRINS_PREFIX SET ATM_CASH_RULE = '" _
        & sRule & "' WHERE BILLING_SP = '" & msBillingSP _
        & "' AND PREFIX = '" & sPrefix & "'"
        
    If moDB.ProcessActionQuery(sSQL) = False Then                    ' K0I0001
        mserror = "cBank:SaveATMRule SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        On Error GoTo 0
        Exit Function
    End If

    SaveATMRule = True
    On Error GoTo 0
    Exit Function

SaveATMRuleError:
    mserror = "cBank:SaveATMRule - " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    On Error GoTo 0

End Function


'**********************************************************************
' Save the client information
' If the Client exists, Update otherwise insert
'**********************************************************************
Public Function SaveClient(sClient As String) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet                                           ' K0I0001
Dim bExists         As Boolean

    On Error GoTo SaveClientError
     
    mserror = ""
    SaveClient = False
     
    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    bExists = ClientExists(sClient)
    
    If Len(mserror) > 0 Then
        On Error GoTo 0
        Exit Function
    End If
     
    If bExists Then
        sSQL = "UPDATE CLIENTS Set" _
            & " NAME = '" & ConvertQuotes(msName) _
            & "', ADDR = '" & ConvertQuotes(msAddr) _
            & "', CITY = '" & ConvertQuotes(msCity) _
            & "', STATE = '" & ConvertQuotes(msState) _
            & "', ZIP = '" & ConvertQuotes(msZip) _
            & "', CONTACT = '" & ConvertQuotes(msContact) _
            & "', PHONE = '" & ConvertQuotes(msPhone) _
            & "', Fax_Number = '" & ConvertQuotes(msFaxNumber) _
            & "', BILLING_SP = '" & ConvertQuotes(msBillingSP) _
            & "', Report_Break_Flag = '" & CStr(miReportBreak) _
            & "', Sub_Client_Xref = '" & CStr(msSubClientXref) _
            & "', CHLookUp_Type = '" & (miSearchType)
        If mbClientActive Then
            sSQL = sSQL & "', ACTIVE = 1"
        Else
            sSQL = sSQL & "', ACTIVE = 0"
        End If
        If mbExcludeFromReport Then
            sSQL = sSQL & ", Exclude_From_Report = 1"
        Else
            sSQL = sSQL & ", Exclude_From_Report = 0"
        End If
        If mbPositiveReports Then
            sSQL = sSQL & ", Positive_Reports = 1"
        Else
            sSQL = sSQL & ", Positive_Reports = 0"
        End If
        If mbAmexIssued Then
            sSQL = sSQL & ", Amex_Issued  = 1"
        Else
            sSQL = sSQL & ", Amex_Issued  = 0"
        End If
        If mbSubClient Then
            sSQL = sSQL & ", Sub_Client_Ind = 1"
        Else
            sSQL = sSQL & ", Sub_Client_Ind = 0"
        End If
        sSQL = sSQL & " WHERE CLIENT = '" & sClient & "'"
    Else
        sSQL = "INSERT INTO CLIENTS (client, name, addr, city, state, zip, contact, " _
            & "phone, Fax_Number, billing_sp, Report_Break_Flag, CHLookUp_Type, " _
            & "active, Exclude_From_Report, Positive_Reports, Sub_Client_Ind, " _
            & "Sub_Client_Xref, Amex_Issued) Values ( " _
            & "'" & msClient & "', " _
            & "'" & ConvertQuotes(msName) & "', " _
            & "'" & ConvertQuotes(msAddr) & "', " _
            & "'" & ConvertQuotes(msCity) & "', " _
            & "'" & ConvertQuotes(msState) & "', " _
            & "'" & ConvertQuotes(msZip) & "', " _
            & "'" & ConvertQuotes(msContact) & "', " _
            & "'" & ConvertQuotes(msPhone) & "', " _
            & "'" & ConvertQuotes(msFaxNumber) & "', " _
            & "'" & ConvertQuotes(msBillingSP) & "', " _
            & CStr(miReportBreak) & ", " _
            & CStr(miSearchType) & ", "
        If mbClientActive Then
            sSQL = sSQL & "'1', "
        Else
            sSQL = sSQL & "'0', "
        End If
        If mbExcludeFromReport Then
            sSQL = sSQL & "'1', "
        Else
            sSQL = sSQL & "'0', "
        End If
        If mbPositiveReports Then
            sSQL = sSQL & "'1', "
        Else
            sSQL = sSQL & "'0', "
        End If
        If mbAmexIssued Then
            sSQL = sSQL & "'1', "
        Else
            sSQL = sSQL & "'0', "
        End If
        If mbSubClient Then
            sSQL = sSQL & "'1', "
        Else
            sSQL = sSQL & "'0', "
        End If
        sSQL = sSQL & "'" & ConvertQuotes(msSubClientXref) & "') "
    End If
    
    If moDB.ProcessActionQuery(sSQL) = False Then                    ' K0I0001
        mserror = "cBank:SaveClient SQL Error" & vbCrLf _
            & sSQL & vbCrLf & moDB.Error                             ' K0I0001
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If
    
    SaveClient = True
    Set rc = Nothing
    On Error GoTo 0
    Exit Function
   
SaveClientError:
    mserror = "cBank:SaveClient Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0
    
End Function


'**********************************************************************
'Check to see if the client already exists
'**********************************************************************
Public Function ClientExists(sClient As String) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet                                           ' K0I0001

    On Error GoTo ClientExistsError

    mserror = ""
    ClientExists = False

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "select count(*) as TheCount from clients" _
            & " where client = '" & sClient & "'"
            
    If moDB.ExecSQLCommand(sSQL, rc) = False Then                    ' K0I0001
        mserror = "cBank:ClientExists SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If
        
    If rc(0) > 0 Then
        ClientExists = True
    End If
    
    Set rc = Nothing
    On Error GoTo 0
    Exit Function
   
ClientExistsError:
    mserror = "cBank:ClientExists Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0
    
End Function


Public Function BillingSPExists(sBillingSP As String, _
                                iCount As Integer) As Boolean       ' K0I0001
Dim sSQL As String
Dim rc As ADODB.RecordSet                                           ' K0I0001

    On Error GoTo BillingSPExistsError

    mserror = ""
    BillingSPExists = False
    
    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "select count(*) as TheCount from clients" _
        & " where billing_sp = '" & sBillingSP & "'"
    
    If moDB.ExecSQLCommand(sSQL, rc) = False Then                    ' K0I0001
        mserror = "cBank:BillingSPExists SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If
    
    If rc!TheCount > 1 Then
        iCount = rc!TheCount
        BillingSPExists = True
    End If
    Set rc = Nothing
    On Error GoTo 0
    Exit Function
   
BillingSPExistsError:
    mserror = "cBank:BillingSPExists Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


'**********************************************************************
'Description:   This function Updates/ a sysprin record
'
'Parameters:    sSysPrin - Sys/Prin of the account being evaluated.
'**********************************************************************
Public Function SaveNewSysPrin() As Boolean
    mserror = ""
    
    msSysPrin = msNewSysPrin
    SaveNewSysPrin = SaveSysPrin

End Function


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


Public Function SysPrinExists(sSysPrin As String, Optional sClient As String) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet

    On Error GoTo SysPrinExistsError

    mserror = ""
    SysPrinExists = False

    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If

    sSQL = ""
    sSQL = "select sys_prin, client from sys_prins" _
           & " where sys_prin   = '" & sSysPrin & "'   "

    If moDB.ExecSQLCommand(sSQL, rc) = False Then
        mserror = "cBank:SysPrinExists SQL Error: " & vbCrLf _
                    & sSQL & vbCrLf & moDB.Error
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If

    If rc.recordCount > 0 Then
        SysPrinExists = True
        sClient = Trim$(rc!client)
    End If

    Set rc = Nothing
    On Error GoTo 0
    Exit Function

SysPrinExistsError:
    mserror = "cBank:SysPrinExists Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


'**********************************************************************
' GetClient gets the client information from the database
'**********************************************************************
Private Function GetClient() As Boolean
Dim rc As ADODB.RecordSet                                           ' K0I0001
Dim sSQL As String

    On Error GoTo GetClientError
    
    mserror = ""
    GetClient = False
    
    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If
    
    sSQL = "SELECT" _
        & " CLIENT, NAME, ADDR, CITY, STATE, ZIP, CONTACT, PHONE," _
        & " BILLING_SP, ACTIVE, Exclude_From_Report, Report_Break_Flag," _
        & " CHLookUp_Type, Fax_Number, Positive_Reports, Sub_Client_Ind," _
        & " Sub_Client_Xref, Amex_issued FROM CLIENTS WHERE CLIENT = '" & msClient & "'"
            
    If moDB.ExecSQLCommand(sSQL, rc) = False Then                    ' K0I0001
        mserror = "cBank:GetClient Fatal SQL Error: " _
            & vbCrLf & sSQL & vbCrLf & moDB.Error                    ' K0I0001
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If
    
    If rc.Eof() Then
        msName = ""
        msAddr = ""
        msCity = ""
        msState = ""
        msZip = ""
        msContact = ""
        msPhone = ""
        msFaxNumber = ""
        mbClientActive = False
        mbExcludeFromReport = False
        mbPositiveReports = False
        mbSubClient = False
        msBillingSP = ""
        msSubClientXref = ""
        miReportBreak = BREAK_NONE
        miSearchType = SEARCH_STANDARD
        mbAmexIssued = False
    Else
        msName = rc!Name & ""
        msAddr = rc!Addr & ""
        msCity = rc!City & ""
        msState = rc!State & ""
        msZip = rc!Zip & ""
        msContact = rc!Contact & ""
        msPhone = rc!Phone & ""
        msFaxNumber = rc!Fax_Number & ""
        mbClientActive = rc!Active
        mbExcludeFromReport = rc!Exclude_From_Report
        mbPositiveReports = rc!Positive_Reports
        mbSubClient = rc!Sub_Client_Ind
        msBillingSP = rc!billing_sp
        msSubClientXref = rc!Sub_Client_Xref
        miReportBreak = CInt(rc!Report_Break_Flag)
        miSearchType = rc!CHLookUp_Type
        mbAmexIssued = rc!Amex_issued
        GetClient = True
    End If
    Set rc = Nothing
    On Error GoTo 0
    Exit Function
   
GetClientError:
    mserror = "cBank:GetClient Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


'**********************************************************************
'Description:   This function calls the GetSysPrin function but passes
'               in a bActive parameter to tell GetSysPrin to retrieve only
'               active or both active & in-active sys/prins.
'
'Parameters:    sSysPrin - Sys/Prin of the account being evaluated.
'               bActive - False (retrieve Active) or True (retrieve Active/Inactive)
'**********************************************************************
Public Function GetAnySysPrin(sSysPrin As String, bActive As Boolean) As Boolean
    msSysPrin = sSysPrin
    GetAnySysPrin = GetSysPrin(bActive)
    
End Function



'**********************************************************************
'Description:   This function gets the client information/rules from
'               the local database and builds the bank object.
'
'Parameters:    sSysPrin - Sys/Prin of the account being evaluated.
'**********************************************************************
Private Function GetSysPrin(Optional bActive As Boolean) As Boolean
Dim sSQL As String
Dim rc As ADODB.RecordSet

    On Error GoTo GetSysPrinError

    mserror = ""
    GetSysPrin = False
    
    If DatabaseConnectionIsMissing Then
        On Error GoTo 0
        Exit Function
    End If

    sSQL = "select cli.Client, cli.name, cli.addr, cli.city, cli.state, " _
        & "cli.zip, cli.contact, cli.phone, cli.Fax_Number, cli.CHLookUp_Type, cli.billing_sp, " _
        & "cli.active as clientactive, cli.Sub_Client_Xref, cli.Amex_issued, sys.cust_type, sys.undeliverable, " _
        & "sys.stat_a, sys.stat_b, sys.stat_c, sys.stat_d, sys.stat_e, sys.stat_f, " _
        & "sys.stat_i, sys.stat_l, sys.stat_o, sys.stat_u, sys.stat_x, sys.stat_z, " _
        & "sys.po_box, sys.non_us, sys.addr_flag, sys.temp_away, sys.rps, sys.session, " _
        & "sys.bad_state, sys.ret_stat, sys.des_stat, sys.a_stat_rch, sys.nm_13, " _
        & "sys.temp_away_atts, sys.report_method, sys.active, sys.hold_days, " _
        & "sys.Special, sys.pin, sys.Notes, sys.entity_cd, sys.Forwarding_Addr " _
      & "from clients cli inner join sys_prins sys on cli.client = sys.client" _
      & " where sys.sys_prin = '" & msSysPrin & "' "
    
    If IsMissing(bActive) Or bActive = False Then   'If bActive = false: get only active
        sSQL = sSQL & " and sys.active = 1"
    End If

    If moDB.ExecSQLCommand(sSQL, rc) = False Then
        mserror = "cBank:GetSysPrin SQL Error" & vbCrLf & sSQL & vbCrLf & moDB.Error
        gLogFile.WriteLog mserror
        Set rc = Nothing
        On Error GoTo 0
        Exit Function
    End If
  
    If Not rc.Eof Then
        msClient = rc!client
        msName = rc!Name & ""
        msAddr = rc!Addr & ""
        msCity = rc!City & ""
        msState = rc!State & ""
        msZip = rc!Zip & ""
        msContact = rc!Contact & ""
        msPhone = rc!Phone & ""
        msFaxNumber = rc!Fax_Number & ""
        mbClientActive = rc!ClientActive
        msBillingSP = rc!billing_sp
        msSubClientXref = rc!Sub_Client_Xref
        mbAmexIssued = rc!Amex_issued
        msCustType = rc!cust_type & ""
        msUnable = rc!Undeliverable & ""
        msStatusA = rc!stat_a & ""
        msStatusB = rc!stat_b & ""
        msStatusC = rc!stat_c & ""
        msStatusD = rc!stat_d & ""
        msStatusE = rc!stat_e & ""
        msStatusF = rc!stat_f & ""
        msStatusI = rc!stat_i & ""
        msStatusL = rc!stat_l & ""
        msStatusO = rc!stat_o & ""
        msStatusU = rc!stat_u & ""
        msStatusX = rc!stat_x & ""
        msStatusZ = rc!stat_z & ""
        msPOBox = rc!po_box & ""
        msNonUS = rc!non_us & ""
        msForwarding = rc!forwarding_addr & ""
        msAddrFlag = rc!addr_flag & ""
        msTempAway = rc!temp_away & ""
        msRPS = rc!RPS & ""
        msSession = rc!Session & ""
        msBadState = rc!bad_state & ""
        msRetStat = rc!ret_stat & ""
        msDesStat = rc!des_stat & ""
        msAStatRch = rc!A_Stat_Rch & ""
        msNM13 = rc!nm_13 & ""
        msTempAwayAtts = rc!temp_away_atts & ""
        msReportMethod = rc!report_method & ""
        mbActive = CBool(rc!Active & "")
        mlHoldDays = rc!Hold_days
        msSpecial = rc!Special & ""
        msPin = rc!Pin & ""
        msNotes = rc!Notes & ""
        mudtEntity_cd = Val(rc!Entity_cd & "")
        GetSysPrin = True
    End If
    Set rc = Nothing
    On Error GoTo 0
    Exit Function
   
GetSysPrinError:
    mserror = "cBank:GetSysPrin Error: " & Err.Number & Err.Description
    gLogFile.WriteLog mserror
    Set rc = Nothing
    On Error GoTo 0

End Function


Private Function DatabaseConnectionIsMissing() As Boolean

    If moDB Is Nothing Then
        mserror = "The database connection has not been established" _
            & vbCrLf & "for the cBank class."
        gLogFile.WriteLog mserror
        DatabaseConnectionIsMissing = True
    Else
        DatabaseConnectionIsMissing = False
    End If
    
End Function

