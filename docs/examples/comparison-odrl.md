# Comparison with ODRL

In this section, we provide a practical comparison between DCPL and ODRL.
We propose use-case examples that highlight the differences in their design, and provide implementations for both ODRL and DCPL to discuss the syntax.

!!! info

    Use-cases, ODRL examples, and discussion points in this section have been extracted from the paper _A critical reflection on ODRL_ by M. G. Kebede et al. (full reference [here](#references)).

## Delegation

Data-sharing scenarios, at times, require individuals to act on behalf of another.
For example, a guardian may be required to act on behalf of a minor; or a carer on behalf of a person unable to grant or deny access to data. Similar patterns occur at the level of institutes. Additionally, research institutes might grant rights to be used by partner institutes under certain conditions to promote a shared research goal.

### Example: Medical Data-Sharing

Suppose `OrganizationX`, an institution in the Netherlands maintaining a registry of patient data, forms a data-sharing agreement with `OrganizationY`, an institution in Belgium. The data-sharing agreement grants `OrganizationY` the permission to access the data and the possibility of delegating this permission to a third party, OrganizationZ. Consequently, the latter will be allowed to have access to the data if `OrganizationY` decides to delegate the permission received from `OrganizationX`.

In ODRL we would model this with an `agreement`:

```json
{
  "@type": "agreement",
  "permission": {
    "assigner": "OrganizationX",
    "assignee": "OrganizationY",
    "action": "transfer",
    "target": "datasetA"
  }
}
```

Here, ownership is assumed to include the possibility of transferring the asset again to someone else (e.g., `OrganizationZ`). This model can be used as a speciﬁcation for non-monotonic delegation, where the grantor loses the permission delegated. However, the same model can not be used to specify monotonic delegation scenarios where the grantor maintains the delegated permission.

This especially becomes problematic to capture the power relationship
between parties; e.g., the party in power has to maintain ownership of the asset,
or “veto” power to either constrain or revoke granted rights, as well as the power
to transfer and/or lose ownership of the asset entirely. For these limitations, we
consider the following alternative model:

```json
[
  {
    "@type": "agreement",
    "permission": {
      "assigner": "OrganizationX",
      "assignee": "OrganizationY",
      "action": "grantUse",
      "target": "datasetA",
      "duty": [{ "action": "nextPolicy", "target": "ex:newPolicy" }]
    }
  },
  {
    "@type": "set",
    "uid": "ex:newPolicy",
    "permission": [{ "action": "read", "target": "datasetA" }]
  }
]
```

In this way, however, usage rights are restricted only to a
third party and not further. In some cases, delegated parties need to be allowed
to delegate. A possible model (possibly abusing the intended use of grantUse)
would be the one expressed below:

```json
[
  {
    "@type": "agreement",
    "permission": {
      "assigner": "OrganizationX",
      "assignee": "OrganizationY",
      "action": "grantUse",
      "target": "datasetA",
      "duty": [{ "action": "nextPolicy", "target": "ex:newGrantPolicy" }]
    }
  },
  {
    "@type": "set",
    "uid": "ex:newGrantPolicy",
    "permission": {
      "action": "grantUse",
      "target": "datasetA",
      "duty": [{ "action": "nextPolicy", "target": "ex:newPolicy" }]
    }
  },
  {
    "@type": "set",
    "uid": "ex:newPolicy",
    "permission": [{ "action": "read", "target": "datasetA" }]
  }
]
```

This extension may enable us to form a hierarchical structure one step further than the previous example, yet it can not represent the full transfer of delegating power to a chain of delegators of unspeciﬁed length.

Other relevant aspects of delegation, e.g. the revocation of rights, also can not be speciﬁed within ODRL. While expressions in ODRL provide terms for specifying deadlines or expiration dates using the constraint class, updating activities are not considered.

In DCPL, unbound delegation to an entire class of [agents](../reference/objects-and-events.md#agents) can be expressed fully by the following.

We first define a `power` for every `organization` agent to perform an action `#share` on some `dataset` which they have access too (i.e. `holder.dataset`) to some `beneficiary` which is also an `organization`. The consequence of this action is that the object `sharing` becomes active with the given parameters.

=== "Code"

    ```
    power {
        holder: organization
        action: #share {
            dataset: holder.dataset
            beneficiary: organization
        }
        consequence: +sharing {
            grantor: holder
            beneficiary: action.beneficiary
            dataset: action.dataset
        }
    }
    ```

=== "JSON"

    ```json
    {
    "position": "power",
    "holder": "organization",
    "action": {
        "reference": "#share",
        "refinement": {
        "dataset": "holder.dataset",
        "beneficiary": "organization"
        }
    },
    "consequence": {
        "plus": {
        "reference": "sharing",
        "refinement": {
            "grantor": "holder",
            "beneficiary": "action.beneficiary",
            "dataset": "action.dataset"
        }
        }
    }
    }
    ```

!!! note

    There is an implicit condition for the action `#share` to be performed and it depends on the [descriptor](../reference/objects-and-events.md#descriptors) `holder.dataset`.

Then we define the concept of `sharing` with a [composite object](../reference/composite-objects.md):

- We activate a [`liberty`](../reference/positions.md#deontic-frames) for the `beneficiary` to `#read` the `dataset`.
- We define a new [`power`](../reference/positions.md#power-frames) for the `beneficiary` to also perform the action `#share`, but only on this specific dataset.

=== "Code"

      ```
      sharing(grantor, beneficiary, dataset) {
          liberty {
              holder: beneficiary
              counterparty: grantor
              action: #read {
                  dataset: dataset
              }
          }

          power {
              holder: beneficiary
              action: #share {
                  dataset: dataset
                  beneficiary: organization
              }
              consequence: +sharing {
                  grantor: beneficiary
                  beneficiary: action.beneficiary
                  dataset: dataset
              }
          }
      }

      ```

=== "JSON"

    ```json
    {
    "compound": "sharing",
    "params": ["grantor", "beneficiary", "dataset"],
    "content": [
        {
        "position": "liberty",
        "holder": "beneficiary",
        "counterparty": "grantor",
        "action": {
            "reference": "#read",
            "refinement": {
            "dataset": "dataset"
            }
        }
        },
        {
        "position": "power",
        "action": {
            "reference": "#share",
            "refinement": {
            "dataset": "dataset",
            "beneficiary": "organization"
            }
        },
        "consequence": {
            "plus": {
            "reference": "sharing",
            "refinement": {
                "grantor": "beneficiary",
                "beneficiary": "action.beneficiary",
                "dataset": "dataset"
            }
            }
        }
        }
    ]
    }
    ```

This not only allows for endless delegation chains, but also easily introduces the concept of _revoking_. We define a [`power`](../reference/positions.md#power-frames) for an `organization` [agent](../reference/objects-and-events.md#agents) to `#revoke_access` to the given `dataset` for any other organization currently sharing it (even indirectly), deactivating the `sharing` object.

=== "Code"

    ```
    power {
        holder: organization
        action: #revoke_access {
            dataset: holder.dataset
            beneficiary: organization
        }
        consequence: -sharing {
            grantor: holder
            beneficiary: action.beneficiary
            dataset: action.dataset
        }
    }
    ```

=== "JSON"

    ```json
    {
        "position": "power",
        "holder": "organization",
        "action": {
            "reference": "#share",
            "refinement": {
                "dataset": "holder.dataset",
                "beneficiary": "organization"
            }
        },
        "consequence": {
            "minus": {
                "reference": "sharing",
                "refinement": {
                    "grantor": "holder",
                    "beneficiary": "action.beneficiary",
                    "dataset": "action.dataset"
                }
            }
        }
    }
    ```

## Semantic for Duty

Duty in its common legal sense is an action
that an agent is obliged to do; otherwise, there will be a violation (see, e.g.,
Hohfeld’s framework of primitive legal concepts[^1]).

### Example: Compensation

In ODRL we could model a simple compensation agreement as following:

```json
{
  "@type": "agreement",
  "obligation": {
    "assigner": "OrganizationX",
    "assignee": "OrganizationZ",
    "action": "compensate",
    "refinement": {
      "leftoperand": "payAmount",
      "operator": "eq",
      "rightOperand": { "@value": "2000.00", "@type": "xsd:decimal" },
      "unit": "http://dbpedia.org/resource/Euro"
    }
  }
}
```

But, the concept of duty is repurposed even for Hohfeld's _institutional power_ when acquiring a permission:

```json
{
  "@type": "agreement",
  "permission": {
    "assigner": "OrganizationX",
    "assignee": "OrganizationY",
    "action": "use",
    "target": "datasetA",
    "duty": {
      "action": "pay",
      "refinement": {
        "leftoperand": "payAmount",
        "operator": "eq",
        "rightoperand": { "@value": "500.00", "@type": "xsd:decimal" },
        "unit": "http://dbpedia.org/resource/Euro"
      }
    }
  }
}
```

This ambiguity is resolved in DCPL with the support for all Hohfeld's normative positions. In practice, we can rewrite the first policy as following:

=== "Code"

    ```
    duty {
        holder: organization_z
        counterparty: organization_x
        action: #compensate {
            amount: 2000.0
            unit: "http://dbpedia.org/resource/Euro"
        }
    }
    ```

=== "JSON"

    ```json
    {
        "position": "duty",
        "holder": "organization_z",
        "counterparty": "organization_x",
        "action": {
            "reference": "#compensate",
            "refinement": {
                "amount": 2000.0,
                "unit": "http://dbpedia.org/resource/Euro"
            }
        }
    }
    ```

And the second one as following:

=== "Code"

    ```
    power {
        holder: organization_y
        action: #use {
            dataset: dataset_a
        }
        consequence: +duty {
            holder: organization_y
            counterparty: organization_x
            action: #compensate {
                amount: 500.0
                unit: "http://dbpedia.org/resource/Euro"
            }
        }
    }
    ```

=== "JSON"

    ```json
    {
        "position": "power",
        "holder": "organization_y",
        "action": {
            "reference": "#use",
            "refinement": {
                "dataset": "dataset_a"
            }
        },
        "consequence": {
            "position": "duty",
            "holder": "organizatyon_y",
            "counterparty": "organization_x",
            "action": {
                "reference": "#compensate",
                "refinement": {
                    "amount": 500.0,
                    "unit": "http://dbpedia.org/resource/Euro"
                }
            }
        }
    }
    ```

Here we explicitate the holder of the `power`, `organization_y` has the _institutional power_ to `#use` the `dataset_a` but this activates a `duty` to `#compensate`.

## Identification of Parties

## Transformational Rules

## Conflict Resolution

## Modeling Outcomes

## Modifying Rules

## Actions

## Policy Lify-Cycle

## References

- Kebede, M. G., Sileno, G., & van Engers, T. (2021). _A Critical Reflection on ODRL_. Lecture Notes in Computer Science (Including Subseries Lecture Notes in Artificial Intelligence and Lecture Notes in Bioinformatics), 13048 LNAI, 48–61. [https://doi.org/10.1007/978-3-030-89811-3_4](https://doi.org/10.1007/978-3-030-89811-3_4)

[^1]: Hohfeld, W.N.: Fundamental legal conceptions as applied in judicial reasoning. Yale Law J. 26(8), 710–770 (1917)
