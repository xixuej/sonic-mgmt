# Test Case Design — Update sfputil CLI supporting operations for CPO with joint mode and Software Control

> **Source HLD (Community PR)**: [`SONiC PR #2317` — *Align transceiver CLIs formatting for common and platform-specific technology extensions*](https://github.com/sonic-net/SONiC/pull/2317)

---

## 1. Test Cases

> **Common terminology** used below:
>
> - **Common CPO fields** — defined in shared maps inside `sonic-utilities` (HLD §5.2). Examples per HLD §5.5: ELS lane bias / voltage / power, CPO transceiver info labels, CPO status labels.
>   - Should test all common CPO fields on all platforms with CPO support
>   - Examples: els_bias_current_monitor<n>(n is channel number(laser)), els_voltage_monitor, etc...
> - **VS (vendor-specific / platform-specific) fields** — supplied at runtime by the platform via `SfpBase.get_platform_specific_transceiver_info_format_map()` / `..._status_format_map()` / `..._dom_format_map()` (HLD §5.3, §5.5).
>   - Should test all VS fields only on Nvidia platforms with CPO support
>   - Examples: els_type, els_type_abbrv_name, els_hardware_rev, etc...

---

### TC-1 [Enhancement required] `sfputil show eeprom -p <cpo_port> -d` includes new CPO/ELS fields (common + VS)

**Pre-conditions**:

- CPO platform with `is_joint_mode=true`; target CPO port link-up.

**Steps**:

```bash
sudo sfputil show eeprom -p <cpo_port> -d
# assert output is parsable with expected exit code
# assert output contains the standard CMIS DOM block (temperature, voltage, txNpower/rxNbias)
# assert output contains the common CPO/ELS fields from the shared map
# assert output contains the PlatformSpecificDomValues section from the platform-specific map
# assert output contains the VS fields from the platform-specific map
```

### TC-2 [Enhancement required] `show interfaces transceiver eeprom -d <cpo_port>` includes new CPO/ELS fields (common + VS)

**Pre-conditions**: same as TC-1.

**Steps**:

```bash
show interfaces transceiver eeprom -d <cpo_port>        # cpo_out
# assert output is parsable with expected exit code
# assert output contains the standard CMIS DOM block (temperature, voltage, txNpower/rxNbias)
# assert output contains the common CPO/ELS fields from the shared map
# assert output contains the PlatformSpecificDomValues section from the platform-specific map
# assert output contains the VS fields from the platform-specific map
```

### TC-3 [Enhancement required] `show interfaces transceiver status <cpo_port>` includes new CPO status fields (common + VS)

**Pre-conditions**: same as TC-1.

**Steps**:

```bash
show interfaces transceiver status <cpo_port>        # cpo_out
# assert output is parsable with expected exit code
# assert output contains the standard CMIS status fields (cmis_state, datapath state, host lane status, etc.)
# assert output contains the common CPO status fields from the shared map
# assert output contains the VS status fields from the platform-specific map
```

### TC-4 [New] `show interfaces transceiver error-status <cpo_port>` includes new CPO error strings

**Pre-conditions**: same as TC-1.

**Steps**:

```bash
show interfaces transceiver error-status                       # all_out  (table-form, all ports)
# assert output is parsable with expected exit code
# assert output contains `Error Status == OK` for all ports when no fault is injected
show interfaces transceiver error-status <cpo_port>            # db_out
# assert output is parsable with expected exit code
# assert output contains `Error Status == OK` for the CPO port when no fault is injected
show interfaces transceiver error-status -hw <cpo_port>        # hw_out
# assert output is parsable with expected exit code
# assert output contains `Error Status == OK` for the CPO port when no fault is injected
```
