# Email Reminder Templates - A006 Provider Evaluation

## Email Reminder Universal (Técnico, SST, MA)

**Subject**: 🔔 Recordatorio: Evaluación de Proveedor Pendiente - [Nombre Proveedor]

---

**Estimado/a [Nombre Destinatario],**

Le recordamos que tiene una **evaluación de proveedor pendiente** de completar.

**📋 Información de la Evaluación:**

| Campo | Valor |
|-------|-------|
| **Proveedor** | [Nombre Proveedor] |
| **Documento** | [Documento Compras] |
| **Fecha Evaluación** | [Fecha Evaluación] |
| **Su Rol** | [Técnico / SST / MA] |
| **Plazo** | [Fecha Límite] |

**✅ Criterios a Evaluar:**

- **Técnico**: Calidad, Oportunidad, Gestión, Ética
- **SST**: Seguridad y Salud en el Trabajo
- **MA**: Medio Ambiente

*(Solo debe completar los criterios correspondientes a su rol)*

---

## Variables para Power Automate

### Campos a Reemplazar:

- `[Nombre Destinatario]`: `nombre_responsable_tecnico` / `nombre_responsable_sst` / `nombre_responsable_ma`
- `[Nombre Proveedor]`: `nombre_proveedor`
- `[Documento Compras]`: `documento_compras`
- `[Fecha Evaluación]`: `fecha_evaluacion`
- `[Su Rol]`: "Técnico" / "SST" / "MA" (según el destinatario)
- `[Fecha Límite]`: Calculado desde `fecha_evaluacion` + período establecido

### Condiciones de Envío:

- **Técnico**: Enviar si `tecnico_submitted = false`
- **SST**: Enviar si `responsable_sst_responde = true` Y `sst_submitted = false`
- **MA**: Enviar si `responsable_ma_responde = true` Y `ma_submitted = false`
- No enviar si `aprobada = true` o `rechazada = true`

### Frecuencia Sugerida:

1. **Primer Recordatorio**: 3 días antes de la fecha límite
2. **Segundo Recordatorio**: 1 día antes de la fecha límite
3. **Recordatorio Final**: 1 día después de la fecha límite
