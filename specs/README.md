# Especificaciones de SimuCenter Next

Una spec es el contrato aprobado que define el problema, el comportamiento,
las restricciones, la seguridad, la aceptación y la verificación de un cambio.
No contiene código productivo.

## Ciclo de vida

`Draft → Requirements Approved → Design Approved → Plan Approved → Ready for Implementation → In Progress → Implemented → Verified`

La aprobación debe ser explícita del usuario. La ausencia de documentos de una
etapa posterior no bloquea las etapas anteriores.

## Documentos por etapa

- **Draft:** `spec.md` está siendo redactado.
- **Requirements Approved:** `spec.md` fue aprobado explícitamente. Ahora
  pueden redactarse `acceptance.md` y `security.md`.
- **Design Approved:** `acceptance.md` y `security.md` fueron aprobados.
  `data-model.md` y los contratos deben estar definidos cuando apliquen. Ahora
  puede redactarse `plan.md`.
- **Plan Approved:** `plan.md` fue aprobado explícitamente. Ahora puede
  crearse `tasks.md`.
- **Ready for Implementation:** `tasks.md` fue revisado y aprobado. Puede
  comenzar la implementación.
- **In Progress:** se están ejecutando las tareas autorizadas.
- **Implemented:** las tareas fueron ejecutadas, pero todavía falta la
  verificación final.
- **Verified:** todos los criterios de aceptación están trazados a pruebas o
  evidencia, las verificaciones pasan y los riesgos restantes están
  documentados.

Una spec no se considera terminada hasta que sus criterios de aceptación y sus
pruebas estén trazados mediante IDs y exista evidencia final.

## Convenciones de IDs

- Requisitos funcionales: `REQ-NNN-NNN`
- Requisitos no funcionales: `NFR-NNN-NNN`
- Criterios de aceptación: `AC-NNN-NNN`
- Controles de seguridad: `SEC-NNN-NNN`
- Tareas: `TASK-NNN-NNN`

Las referencias entre requisitos, aceptación, seguridad, tareas y pruebas deben
conservar estos IDs sin reutilizarlos dentro de la spec.

## Plantilla

`_template/` contiene la estructura mínima para una nueva spec. Copiarla a una
carpeta con el identificador y nombre de la feature únicamente cuando exista
una iniciativa autorizada.
