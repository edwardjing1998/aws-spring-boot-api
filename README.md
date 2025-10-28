+ {tabIndex === 1 && (
+   <EditReMailOptions
+     key={`remail-${selectedData?.sysPrin ?? ''}`}
+     selectedData={selectedData}
+     setSelectedData={setSelectedData}
+     isEditable={isEditable}
+     onChangeGeneral={onChangeGeneral}   // 👈 forward the focused updater
+   />
+ )}
