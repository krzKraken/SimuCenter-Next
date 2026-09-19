# Tareas de <FEATURE_NAME>

Referencia: `<SPEC_ID>`

## Tareas

### TASK-NNN-NNN — <TASK_TITLE>

- **Descripción:** <TASK_DESCRIPTION>
- **Referencias:** `REQ-NNN-NNN`, `AC-NNN-NNN`, `SEC-NNN-NNN`
- **Dependencias:** `<TASK_IDS_OR_NONE>`
- **Paralelizable:** `<YES_OR_NO>`
- **Verificación requerida:** `<COMMAND_OR_TEST_REFERENCE>`
- **Estado:** `Pendiente`

## Dependencias y paralelismo

| Tarea | Depende de | Puede ejecutarse en paralelo con | Motivo |
|---|---|---|---|
| `TASK-NNN-NNN` | `<TASK_IDS_OR_NONE>` | `<TASK_IDS_OR_NONE>` | `<RATIONALE>` |

## Checklist de cierre

- [ ] Todas las tareas `TASK-*` están implementadas o tienen una excepción
      aprobada.
- [ ] Cada tarea referencia requisitos, aceptación y controles de seguridad
      aplicables.
- [ ] Las pruebas requeridas fueron ejecutadas y reportadas.
- [ ] Cada `AC-*` tiene al menos una prueba y evidencia final.
- [ ] Se verificó autorización y aislamiento cross-tenant.
- [ ] Migraciones, observabilidad y documentación fueron revisadas cuando
      aplican.
- [ ] Riesgos restantes están documentados.
