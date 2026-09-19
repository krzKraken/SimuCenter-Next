# Seguridad de <FEATURE_NAME>

Referencia: `<SPEC_ID>`

## Activos

- `<ASSET>` — <IMPACT_IF_COMPROMISED>

## Datos sensibles

- `<DATA_CLASSIFICATION_OR_NONE>`

## Trust boundaries

- `<BOUNDARY>`: <CROSSING_AND_REQUIRED_CONTROLS>

## Amenazas

| ID | Activo | Amenaza | Impacto | Probabilidad | Mitigación o referencia |
|---|---|---|---|---|---|
| `<THREAT_ID>` | `<ASSET>` | `<THREAT>` | `<IMPACT>` | `<PROBABILITY>` | `<MITIGATION>` |

## Controles SEC-*

- **SEC-NNN-NNN:** <SECURITY_CONTROL>

## Autorización

- Recurso: `<RESOURCE>`
- Acción: `<ACTION>`
- Scope requerido: `<SCOPE>`
- Ownership requerido: `<OWNERSHIP_RULE>`
- Comportamiento por defecto: denegar.

## Aislamiento tenant

- `tenant_id`: `<REQUIRED_OR_GLOBAL_JUSTIFICATION>`
- Política PostgreSQL RLS: `<POLICY_REQUIREMENT>`
- Contexto tenant y transacción: `<REQUIREMENT>`
- Pruebas cross-tenant negativas: `<TEST_REFERENCE>`

## Logging

### Prohibido

- Contraseñas, tokens, secretos, credenciales, cuerpos sensibles y datos no
  necesarios para operar o auditar.

### Permitido

- Request ID, actor, tenant, acción, recurso, resultado y metadatos mínimos
  necesarios, conforme a la política de privacidad.

## Pruebas negativas

- `<UNAUTHORIZED_ACCESS_TEST>`
- `<CROSS_TENANT_ACCESS_TEST>`
- `<INVALID_SCOPE_OR_OWNERSHIP_TEST>`
- `<INPUT_ABUSE_OR_REPLAY_TEST>`

## Referencias ASVS

- `<ASVS_REQUIREMENT_OR_NONE>`

## Riesgos aceptados

- `<ACCEPTED_RISK_OR_NONE>`
- Responsable: `<RISK_OWNER_OR_NONE>`
- Fecha de expiración: `<YYYY-MM-DD_OR_NONE>`
- Plan de eliminación: `<REMOVAL_PLAN_OR_NONE>`

## Aprobación de diseño de seguridad

- **Estado:** `Pendiente`
- **Aprobado por:** `<USER_OR_NONE>`
- **Fecha:** `<YYYY-MM-DD_OR_NONE>`
- **Comentario o referencia:** `<APPROVAL_REFERENCE_OR_NONE>`

La existencia de este archivo no constituye aprobación. Ningún riesgo de
severidad alta o crítica puede aceptarse implícitamente. Todo riesgo aceptado
requiere responsable, fecha de expiración, justificación y aprobación explícita.
