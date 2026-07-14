CREATE PROCEDURE [dbo].[PA_NOTAS_PLANILLA]  
    @CLASE     VARCHAR(20),    
    @N_NOTA_ID INT,    
    @GRUPO     VARCHAR(20) = NULL    
AS    
BEGIN    
    SET NOCOUNT ON;    
    
    DECLARE @PLANTILLANOTAS TABLE (    
        S_CLA_CODIGO VARCHAR(20),    
        S_ALU_CODIGO VARCHAR(20),    
        S_ALU_APATERNO VARCHAR(150),    
        S_ALU_AMATERNO VARCHAR(150),    
        S_ALU_NOMBRES VARCHAR(150),    
        S_DEP_NOMBRE VARCHAR(80),    
        N_MADE_NRO_VECES INT,    
        N_ALNODE_VALOR DECIMAL(5,2),    
        S_MADE_ESTADO VARCHAR(5),    
        N_NOTA_MOTIVO_ID INT,    
        b_alnode_evaluacion_investigacion BIT,    
        b_alnode_sancion BIT,    
        b_alno_sancion BIT,  
        b_dpi_aplicado BIT,  
  s_sem_tipo VARCHAR(2)  
    );    
    
    IF EXISTS (SELECT 1 FROM dbo.clase WHERE s_cla_codigo = @CLASE)    
    BEGIN    
        INSERT INTO @PLANTILLANOTAS    
        (    
            S_CLA_CODIGO,    
            S_ALU_CODIGO,    
            S_ALU_APATERNO,    
            S_ALU_AMATERNO,    
            S_ALU_NOMBRES,    
            S_DEP_NOMBRE,    
            N_MADE_NRO_VECES,    
            N_ALNODE_VALOR,    
            S_MADE_ESTADO,    
            N_NOTA_MOTIVO_ID,    
            b_alnode_evaluacion_investigacion,    
            b_alnode_sancion,    
            b_alno_sancion,  
            b_dpi_aplicado,  
   s_sem_tipo  
        )    
        SELECT DISTINCT    
            D.S_CLA_CODIGO,    
            A.S_ALU_CODIGO,    
            A.S_ALU_APATERNO,    
            A.S_ALU_AMATERNO,    
            A.S_ALU_NOMBRES,    
            DEP.S_DEP_NOMBRE,    
            D.N_MADE_NRO_VECES,    
            ROUND(C.N_ALNODE_VALOR, 2) AS N_ALNODE_VALOR,    
            D.S_MADE_ESTADO,    
            F.n_nota_motivo_id AS N_NOTA_MOTIVO_ID,    
            ISNULL(C.b_alnode_evaluacion_investigacion, 0) AS b_alnode_evaluacion_investigacion,    
            ISNULL(C.b_alnode_sancion, 0) AS b_alnode_sancion,    
            ISNULL(B.b_alno_sancion, 0) AS b_alno_sancion,  
            CASE  
                WHEN EXISTS (  
                    SELECT 1 FROM dpi_alumno da  
                    WHERE da.s_alu_codigo   = A.S_ALU_CODIGO  
                      AND da.s_cla_codigo   = B.S_CLA_CODIGO  
                      AND da.b_dpi_aplicado = 1  
                      AND da.n_alumno_nota_detalle_id = C.N_ALUMNO_NOTA_DETALLE_ID  
                )  
                THEN CAST(1 AS BIT)  
                ELSE CAST(0 AS BIT)  
            END AS b_dpi_aplicado,  
   S.s_sem_tipo  
        FROM dbo.alumno A    
        INNER JOIN dbo.matricula_detalle D    
            ON A.S_ALU_CODIGO = D.S_ALU_CODIGO    
           AND D.S_CLA_CODIGO = @CLASE    
        INNER JOIN dbo.semestre S    
            ON S.s_sem_codigo = D.s_sem_codigo    
           AND S.n_sem_estado = 1    
        INNER JOIN dbo.alumno_nota B    
            ON A.S_ALU_CODIGO = B.S_ALU_CODIGO    
           AND B.n_semestre_id = S.n_semestre_id    
        INNER JOIN dbo.alumno_nota_detalle C    
            ON B.N_ALUMNO_NOTA_ID = C.N_ALUMNO_NOTA_ID    
        INNER JOIN dbo.clase CL    
            ON CL.s_cla_codigo = D.s_cla_codigo    
        INNER JOIN dbo.departamento DEP    
            ON DEP.s_dep_codigo = CL.s_dep_codigo    
        LEFT JOIN dbo.nota_motivo F    
            ON C.n_nota_motivo_id = F.n_nota_motivo_id    
        WHERE B.S_CLA_CODIGO = @CLASE    
          AND C.N_NOTA_ID = @N_NOTA_ID    
    
        UNION ALL    
    
        SELECT DISTINCT    
            B.S_CLA_CODIGO,    
            A.S_ALU_CODIGO,    
            A.S_ALU_APATERNO,    
            A.S_ALU_AMATERNO,    
            A.S_ALU_NOMBRES,    
            DEP.S_DEP_NOMBRE,    
            B.N_MADE_NRO_VECES,    
            NULL AS N_ALNODE_VALOR,    
            B.S_MADE_ESTADO,    
            NULL AS N_NOTA_MOTIVO_ID,    
            0 AS b_alnode_evaluacion_investigacion,    
            0 AS b_alnode_sancion,    
            ISNULL(AN.b_alno_sancion, 0) AS b_alno_sancion,  
            CAST(0 AS BIT) AS b_dpi_aplicado,  
   E.s_sem_tipo  
        FROM dbo.alumno A    
        INNER JOIN dbo.matricula_detalle B    
            ON A.S_ALU_CODIGO = B.S_ALU_CODIGO    
        INNER JOIN dbo.clase D    
            ON B.S_CLA_CODIGO = D.S_CLA_CODIGO    
        INNER JOIN dbo.semestre E    
            ON D.N_SEMESTRE_ID = E.N_SEMESTRE_ID    
           AND E.n_sem_estado = 1    
           AND B.s_sem_codigo = E.s_sem_codigo    
        LEFT JOIN dbo.alumno_nota AN    
            ON A.S_ALU_CODIGO = AN.S_ALU_CODIGO    
           AND AN.S_CLA_CODIGO = B.S_CLA_CODIGO    
           AND AN.n_semestre_id = E.n_semestre_id    
        INNER JOIN dbo.departamento DEP    
            ON DEP.s_dep_codigo = D.s_dep_codigo    
        WHERE B.S_CLA_CODIGO = @CLASE    
          AND A.S_ALU_CODIGO NOT IN    
          (    
              SELECT A2.S_ALU_CODIGO    
              FROM dbo.alumno A2    
              INNER JOIN dbo.matricula_detalle D2    
                  ON A2.S_ALU_CODIGO = D2.S_ALU_CODIGO    
                 AND D2.S_CLA_CODIGO = @CLASE    
              INNER JOIN dbo.semestre S2    
                  ON S2.s_sem_codigo = D2.s_sem_codigo    
                 AND S2.n_sem_estado = 1    
              INNER JOIN dbo.alumno_nota B2    
                  ON A2.S_ALU_CODIGO = B2.S_ALU_CODIGO    
                 AND B2.n_semestre_id = S2.n_semestre_id    
              INNER JOIN dbo.alumno_nota_detalle C2    
                  ON B2.N_ALUMNO_NOTA_ID = C2.N_ALUMNO_NOTA_ID    
              WHERE B2.S_CLA_CODIGO = @CLASE    
                AND C2.N_NOTA_ID = @N_NOTA_ID    
          );    
    END;    
    
    IF @GRUPO IS NOT NULL AND LEN(@GRUPO) > 0    
    BEGIN    
        RETURN;    
    END;    
    
    SELECT    
        A.S_ALU_CODIGO,  
  ISNULL((SELECT s_aldaus_cod_alumno FROM alumno_datos_usuario ADU WHERE ADU.s_alu_codigo = A.S_ALU_CODIGO AND ADU.s_aldaus_nivel = A.s_sem_tipo),'-') AS S_ALU_SOCRATES,  
        A.S_ALU_APATERNO,    
        A.S_ALU_AMATERNO,    
        A.S_ALU_NOMBRES,    
        A.S_DEP_NOMBRE,    
        A.N_MADE_NRO_VECES,    
        ROUND(A.N_ALNODE_VALOR, 2) AS NOTA,    
        RTRIM(LTRIM(A.S_MADE_ESTADO)) AS S_MADE_ESTADO,    
        0 AS B_ARS_ESTADO,    
        A.N_NOTA_MOTIVO_ID,    
        ISNULL(A.b_alnode_evaluacion_investigacion, 0) AS b_alnode_evaluacion_investigacion,    
        ISNULL(A.b_alnode_sancion, 0) AS b_alnode_sancion,    
        ISNULL(A.b_alno_sancion, 0) AS b_alno_sancion,  
        ISNULL(A.b_dpi_aplicado, 0)       AS b_dpi_aplicado  
    FROM @PLANTILLANOTAS A    
    ORDER BY A.S_ALU_APATERNO, A.S_ALU_AMATERNO, A.S_ALU_NOMBRES;    
END;  