       onSave?.({
         reportName: form.reportName ?? '',
         reportId: form.reportId ?? null,
         receive: String(form.receive ?? '0'),
         destination: String(form.destination ?? '0'),
         fileText: String(form.fileText ?? '0'),
         email: String(form.email ?? '0'),
         password: form.password ?? '',
         emailBodyTx: form.emailBodyTx ?? '',
       });
