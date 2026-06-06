# McareSeguros

lowchart TD
    %% Leyenda: P = proceso, E = evento, D = decision/regla, R = reporte o bandeja

    A["Asegurado / empleado carga solicitud"] --> P1["P1 Crear evento y validar poliza, saldo y matricula"]
    P1 --> E1(("E1 EventoCreado"))
    E1 --> DBE[("BD SQL Eventos")]
    E1 --> P2["P2 Cargar documentos PDF / BMP / JPG"]
    P2 --> E2(("E2 DocumentoCargado"))
    E2 --> REPO[("SharePoint / repositorio documental")]
    E2 --> P3["P3 Procesar OCR en cluster cloud"]

    P3 -->|exito| E3(("E3 OCRCompletado JSON generado"))
    P3 -->|error o baja confianza| E3B(("E3b OCRFallido / CamposNoLeidos"))
    E3 --> P4["P4 Leer JSON y normalizar datos"]
    P4 --> DBD[("BD SQL cloud Datos por documento")]
    P4 --> D1{"D1 Datos criticos completos?"}

    D1 -->|No| E4(("E4 DocumentoRequiereProcesoManual"))
    E4 --> BOP["Bandeja de operadores"]
    BOP --> P5["P5 Operador visualiza documento y completa datos"]
    P5 --> E5(("E5 DocumentoCorregidoManual"))
    E5 -. actualiza DB .-> DBD
    E5 -. reevalua .-> D1

    D1 -->|Si| E6(("E6 TodosLosDocumentosCompletos"))
    E6 --> P6["P6 Motor de reglas: documentacion, fechas, montos y saldo"]
    P6 --> D2{"D2 Falta documentacion obligatoria?"}
    D2 -->|Si| E7(("E7 EventoIncompleto"))
    D2 -->|No| D3{"D3 Monto reclamado <= facturas y saldo?"}
    D3 -->|No cumple| E8(("E8 EventoRechazado"))
    D3 -->|Cumple| E9(("E9 EventoRequiereAprobacion"))

    E9 --> BREV["Bandeja de revisores"]
    BREV --> P7["P7 Revisor verifica documentos y firmas"]
    P7 --> D4{"D4 Revisor aprueba?"}
    D4 -->|No| E8
    D4 -->|Si| E10(("E10 EventoAprobado"))

    E10 --> R1["Informe tiempo real: eventos listos para pago"]
    R1 --> P8["P8 Finanzas realiza pago"]
    P8 --> E11(("E11 PagoRealizado"))
    E11 --> P9["P9 Actualizar saldo disponible"]
    P9 --> D5{"D5 Saldo disponible < USD 2000?"}
    D5 -->|Si| E12(("E12 SaldoMenorA2000"))
    E12 --> R2["Informe comercial: asegurados con saldo menor a USD 2000"]

    E7 --> R3["Informe tiempo real: eventos incompletos o rechazados"]
    E8 --> R3
    R3 --> P10["Ejecutivo contacta al cliente"]
    P10 -. documentacion faltante .-> P2

    E3B --> R4["Batch desarrollo: documentos no leidos por completo"]
    E4 --> R4

    DBE --> R5["Batch manana: eventos ingresados del dia anterior + monto total"]
    DBE --> R6["Batch fin de dia: eventos del dia anterior que siguen ingresados"]

    classDef proceso fill:#fff4d6,stroke:#b7791f,stroke-width:1px,color:#1f2937;
    classDef evento fill:#e8f0fe,stroke:#2f5fb3,stroke-width:1px,color:#1f2937;
    classDef decision fill:#ffe8e8,stroke:#b42318,stroke-width:1px,color:#1f2937;
    classDef storage fill:#eaf7ea,stroke:#2e7d32,stroke-width:1px,color:#1f2937;
    classDef reporte fill:#f3e8ff,stroke:#6f42c1,stroke-width:1px,color:#1f2937;

    class A,P1,P2,P3,P4,P5,P6,P7,P8,P9,P10 proceso;
    class E1,E2,E3,E3B,E4,E5,E6,E7,E8,E9,E10,E11,E12 evento;
    class D1,D2,D3,D4,D5 decision;
    class DBE,DBD,REPO storage;
    class BOP,BREV,R1,R2,R3,R4,R5,R6 reporte;
