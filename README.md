# rabit e2e fixtures

Git fixtures for rabit's end-to-end CI job tests (`com.rabit.integration.cijob.triggerbuild` in rabit).
SFDX (source-format) fixtures live in `rabit-e2e-sfdx-fixtures`, which rabit registers as SFDX-enabled.
Each branch is one fixture; tests point at it through `application-it.yaml` (`IT_GIT_BRANCH` and friends).

| Branch | Layout | Used by |
|---|---|---|
| `vc2sf-metadata` | Metadata format at the repository root: `classes/`, a permission set, a profile (class access, login IP range, user permission) and `package.xml`; a class added and deleted again; a `.forceignore` naming a developer-only class | `Vc2SfStartBuildET`, `VcPackageStartBuildET` |
| `vc-sca-new` | `vc2sf-metadata`'s first commit plus one commit that modifies `RabitE2EClassB` and adds `RabitE2EClassE`, for Apex PMD on newly added types | `VcPackageStartBuildET` |
| `vc2sf-destructive` | `vc2sf-metadata` plus `ApexClassTest3` (a copy of the class in the e2e org), which the next commit deletes while changing `RabitE2EClassA`, for Run destructive changes | `Vc2SfStartBuildET` |
| `vc2sf-visibility` | `vc2sf-metadata` with the profile also granting access to `RabitE2EPhantomClass`, a class no org or package has, for Ignore missing visibility settings | `Vc2SfStartBuildET` |
| `vc2sf-vlocity` | `vc2sf-metadata` plus a DataRaptor and an OmniScript datapack in `autorabit_alldefault_vlocity_build`, for the Vlocity build | `Vc2SfStartBuildET` |
| `sfsource-autocommit` | Where the org-sourced auto-commit jobs push; each test recreates it from `main` first | `SfWithAcStartBuildET`, `Sf2SfWithAcStartBuildET` |

Conventions:
- One fixture per branch, named after the job type and the variant it exercises.
- Keep each fixture small and self-contained, so it validates against a clean Developer org.
- Tests only run validate-only (check-only) deploys, so nothing here is ever saved to an org; only
  `sfsource-autocommit` gets commits from AutoRABIT.
- Baseline revisions in `application-it.yaml` are commit hashes on these branches, so never rewrite their history.
- When a branch's contents change, update the fixture keys in rabit's `application-it.yaml`.
