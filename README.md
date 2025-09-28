// src/views/rapid-admin-edit/daily-activity/ActionCell.jsx
import React from 'react'
import { IconButton } from '@mui/material'
import EditOutlinedIcon from '@mui/icons-material/EditOutlined'
import AddCircleOutlineIcon from '@mui/icons-material/AddCircleOutline'
import DeleteOutlineOutlinedIcon from '@mui/icons-material/DeleteOutlineOutlined'

const ActionCell = (props) => {
  const row = props.data || {}
  const { onEdit, onCreate, onDelete } = props // <- passed in via cellRendererParams
  return (
    <div style={{ display: 'flex', gap: 8, alignItems: 'center' }}>
      <IconButton size="small" onClick={() => onEdit(row)}>
        <EditOutlinedIcon fontSize="inherit" />
      </IconButton>
      <IconButton size="small" onClick={() => onCreate(row)}>
        <AddCircleOutlineIcon fontSize="inherit" />
      </IconButton>
      <IconButton size="small" onClick={() => onDelete(row)}>
        <DeleteOutlineOutlinedIcon fontSize="inherit" />
      </IconButton>
    </div>
  )
}

export default ActionCell
