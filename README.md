USE [Rapid3]
GO
/****** Object:  StoredProcedure [dbo].[Usp_Delete_From_Labels]    Script Date: 6/27/2025 2:35:34 PM ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER PROCEDURE [dbo].[Usp_Delete_From_Labels]
@sCaseNo char(16)
AS
delete from labels where case_number = @sCaseNo
