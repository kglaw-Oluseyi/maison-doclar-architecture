# Architectural Fossils Register
Historical implementation artifacts that encode constitutional decisions. A fossil without its story is just old code; each entry preserves the *why*.

| Fossil | Location | Decision it encodes |
|---|---|---|
| `retire_rsvp_no_response` migration | aperture/prisma/migrations | No-response is an escalation flag, never an RSVP state. Constitutional. |
| `verify-allocator-suggestion-event-scope.ts` | aperture/scripts | Cross-event contamination occurred once; event-scope verification is law. |
| `guest_list_import_lock` migration | aperture/prisma/migrations | Imports and allocation must never race. |
| `print_consent_report_dispatch` migration | aperture/prisma/migrations | Consent gates physical artifacts, not just digital ones. |
| `prompt58_allocator_rebuild` migration | aperture/prisma/migrations | Allocator v1's limits are known; the rebuild was earned, not aesthetic. |
| `purge-biometrics` endpoint | facegate/app/api/admin | Biometric data is deletable by design; deletion is an admin right, not a request. |
| One-time-reveal API keys | gateway docs + code | Secrets are shown once; possession is not re-derivable. |
| `Dockerfile.focus-check` + railway.focus-check.toml | aperture | Monitoring deserves its own deployable; the watchdog must not share the patient's fate. |
