---
applyTo: "flows/**"
---
# Fejlsøgning af cloud flows
- Bed om fejlbesked og fejlet action fra run history, hvis den ikke er givet.
- Struktur i svar: 1) Årsag 2) Rettelse (konkret expression/indstilling) 3) Hvordan det testes.
- Rettelser udføres i maker-portalen. Foreslå ikke håndredigering af flow-JSON uden at fremhæve risikoen ved import.
- Tjek typisk: null-håndtering, expressions, run after-konfiguration, pagination/threshold, concurrency, connection references.
