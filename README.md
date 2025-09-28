// src/views/rapid-admin-edit/daily-activity/ActionCell.jsx
import React from 'react'
import { IconButton } from '@mui/material'
import EditOutlinedIcon from '@mui/icons-material/EditOutlined'
import VisibilityOutlinedIcon from '@mui/icons-material/VisibilityOutlined' // ← review/eye icon
import DeleteOutlineOutlinedIcon from '@mui/icons-material/DeleteOutlineOutlined'

const ActionCell = (props) => {
  const row = props.data || {}
  const { onEdit, onReview, onDelete } = props // ← use onReview instead of onCreate

  // helper to avoid row selection when clicking icons
  const safe = (fn) => (e) => { e?.stopPropagation?.(); fn?.(row) }

  return (
    <div style={{ display: 'flex', gap: 8, alignItems: 'center' }}>
      <IconButton size="small" onClick={safe(onEdit)} title="Edit">
        <EditOutlinedIcon fontSize="inherit" />
      </IconButton>

      <IconButton size="small" onClick={safe(onReview)} title="Review">
        <VisibilityOutlinedIcon fontSize="inherit" />
      </IconButton>

      <IconButton size="small" onClick={safe(onDelete)} title="Delete">
        <DeleteOutlineOutlinedIcon fontSize="inherit" />
      </IconButton>
    </div>
  )
}

export default ActionCell
