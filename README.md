'**********************************************************************
'Author:        Jim Wood
'Date:          20 Oct 98
'
'Description:   Move a sysprin
'
'               Ask for a new Client number
'                    If it is valid, an update is performed
'               Save the new SYS/PRIN INFO
'**********************************************************************
Private Sub SysPrinMove()
Dim sSQL                    As String
Dim sOriginal               As String
Dim sNew                    As String
 
    Load frmEnterSysPrin
    frmEnterSysPrin.Label1.Caption = "Enter new Client Number:"
    frmEnterSysPrin.Caption = "Move Sys/Prin"
 
    'Ask for SYS/PRIN
    frmEnterSysPrin.Show vbModal
 
    'Did operator click cancel?
    If Me.Tag = "CANCEL" Then
        Exit Sub
    Else
    'did we get anything back as the number?
        If Me.Tag <> "" Then
            'Copy the current SYS/PRIN to the new Client number
            sOriginal = moBankInfo.client
            sNew = Me.Tag
            moBankInfo.client = sNew
 
            If moBankInfo.ClientExists(Me.Tag) = False Then        ' K0I0001
                MsgBox "Client " & sNew & " does not exist.", _
                    vbInformation + vbOKOnly, "Invalid Client Number"
                Exit Sub
            End If
 
            'Save the sysprin info under the new client
            Call moBankInfo.SaveSysPrin                             ' K0I0001
        End If
 
        FindClient sOriginal                ' Update original client TreeView entry
        'point to the new client
        Call FindSysPrin(moBankInfo.SysPrin)
    End If
 
End Sub
 




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
Dim sOriginal               As String'**********************************************************************
'Author:        Jim Wood
'Date:          20 Oct 98
'
'Description:   Move a sysprin
'
'               Ask for a new Client number
'                    If it is valid, an update is performed
'               Save the new SYS/PRIN INFO
'**********************************************************************
Private Sub SysPrinMove()
Dim sSQL                    As String
Dim sOriginal               As String
Dim sNew                    As String
 
    Load frmEnterSysPrin
    frmEnterSysPrin.Label1.Caption = "Enter new Client Number:"
    frmEnterSysPrin.Caption = "Move Sys/Prin"
 
    'Ask for SYS/PRIN
    frmEnterSysPrin.Show vbModal
 
    'Did operator click cancel?
    If Me.Tag = "CANCEL" Then
        Exit Sub
    Else
    'did we get anything back as the number?
        If Me.Tag <> "" Then
            'Copy the current SYS/PRIN to the new Client number
            sOriginal = moBankInfo.client
            sNew = Me.Tag
            moBankInfo.client = sNew
 
            If moBankInfo.ClientExists(Me.Tag) = False Then        ' K0I0001
                MsgBox "Client " & sNew & " does not exist.", _
                    vbInformation + vbOKOnly, "Invalid Client Number"
                Exit Sub
            End If
 
            'Save the sysprin info under the new client
            Call moBankInfo.SaveSysPrin                             ' K0I0001
        End If
 
        FindClient sOriginal                ' Update original client TreeView entry
        'point to the new client
        Call FindSysPrin(moBankInfo.SysPrin)
    End If
 
End Sub
 
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




package rapid.service.sysprin;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import rapid.dto.sysPrin.SysPrinDTO;
import rapid.model.sysprin.SysPrin;
import rapid.model.sysprin.key.SysPrinId;
import rapid.repository.sysprin.SysPrinRepository;
import rapid.sysPrin.mapper.SysPrinMapper;

import java.util.Optional;

@Service
@RequiredArgsConstructor
public class SysPrinMoveAllService {

    private final SysPrinRepository sysPrinRepository;
    private final SysPrinMapper sysPrinMapper;

    /**
     * Try to update the client of a SysPrin.
     * Returns Optional.empty() if the old SysPrin does not exist.
     */
    @Transactional
    public Optional<SysPrin> updateClient(String oldClientId, String sysPrin, String newClientId) {
        SysPrinId oldId = new SysPrinId(oldClientId, sysPrin);

        return sysPrinRepository.findById(oldId).map(oldEntity -> {
            SysPrinDTO dto = sysPrinMapper.toDto(oldEntity);
            dto.setClient(newClientId);

            SysPrin newEntity = sysPrinMapper.toEntity(dto);
            newEntity.setId(new SysPrinId(newClientId, sysPrin));

            sysPrinRepository.delete(oldEntity);
            return sysPrinRepository.save(newEntity);
        });
    }
}









