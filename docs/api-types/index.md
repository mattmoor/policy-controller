

# API Documentation (v1beta1)

> This document is automatically generated from the API definition in the code.

## Table of Contents
* [Attestation](#attestation)
* [Authority](#authority)
* [ClusterImagePolicy](#clusterimagepolicy)
* [ClusterImagePolicyList](#clusterimagepolicylist)
* [ClusterImagePolicySpec](#clusterimagepolicyspec)
* [ConfigMapReference](#configmapreference)
* [Identity](#identity)
* [ImagePattern](#imagepattern)
* [KeyRef](#keyref)
* [KeylessRef](#keylessref)
* [MatchResource](#matchresource)
* [Policy](#policy)
* [Source](#source)
* [StaticRef](#staticref)
* [TLog](#tlog)

## Attestation

Attestation defines the type of attestation to validate and optionally apply a policy decision to it. Authority block is used to verify the specified attestation types, and if Policy is specified, then it's applied only after the validation of the Attestation signature has been verified.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| name | Name of the attestation. These can then be referenced at the CIP level policy. | string | true |
| predicateType | PredicateType defines which predicate type to verify. Matches cosign verify-attestation options. | string | true |
| policy | Policy defines all of the matching signatures, and all of the matching attestations (whose attestations are verified). | [Policy](#policy) | false |

[Back to TOC](#table-of-contents)

## Authority

The authorities block defines the rules for discovering and validating signatures.  Signatures are cryptographically verified using one of the \"key\" or \"keyless\" fields. When multiple authorities are specified, any of them may be used to source the valid signature we are looking for to admit an image.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| name | Name is the name for this authority. Used by the CIP Policy validator to be able to reference matching signature or attestation verifications. If not specified, the name will be authority-<index in array> | string | true |
| key | Key defines the type of key to validate the image. | [KeyRef](#keyref) | false |
| keyless | Keyless sets the configuration to verify the authority against a Fulcio instance. | [KeylessRef](#keylessref) | false |
| static | Static specifies that signatures / attestations are not validated but instead a static policy is applied against matching images. | [StaticRef](#staticref) | false |
| source | Sources sets the configuration to specify the sources from where to consume the signatures. | [][Source](#source) | false |
| ctlog | CTLog sets the configuration to verify the authority against a Rekor instance. | [TLog](#tlog) | false |
| attestations | Attestations is a list of individual attestations for this authority, once the signature for this authority has been verified. | [][Attestation](#attestation) | false |

[Back to TOC](#table-of-contents)

## ClusterImagePolicy



| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| metadata |  | [metav1.ObjectMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#objectmeta-v1-meta) | true |
| spec | Spec holds the desired state of the ClusterImagePolicy (from the client). | [ClusterImagePolicySpec](#clusterimagepolicyspec) | true |

[Back to TOC](#table-of-contents)

## ClusterImagePolicyList

ClusterImagePolicyList is a list of ClusterImagePolicy resources

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| metadata |  | [metav1.ListMeta](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#listmeta-v1-meta) | true |
| items |  | [][ClusterImagePolicy](#clusterimagepolicy) | true |

[Back to TOC](#table-of-contents)

## ClusterImagePolicySpec

ClusterImagePolicySpec defines a list of images that should be verified

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| images | Images defines the patterns of image names that should be subject to this policy. | [][ImagePattern](#imagepattern) | true |
| authorities | Authorities defines the rules for discovering and validating signatures. | [][Authority](#authority) | true |
| policy | Policy is an optional policy that can be applied against all the successfully validated Authorities. If no authorities pass, this does not even get evaluated, as the Policy is considered failed. | [Policy](#policy) | false |
| mode | Mode controls whether a failing policy will be rejected (not admitted), or if errors are converted to Warnings. enforce - Reject (default) warn - allow but warn | string | false |
| match | Match allows selecting resources based on their properties. | [][MatchResource](#matchresource) | false |

[Back to TOC](#table-of-contents)

## ConfigMapReference

ConfigMapReference is cut&paste from SecretReference, but for the life of me couldn't find one in the public types. If there's one, use it.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| name | Name is unique within a namespace to reference a configmap resource. | string | false |
| namespace | Namespace defines the space within which the configmap name must be unique. | string | false |

[Back to TOC](#table-of-contents)

## Identity

Identity may contain the issuer and/or the subject found in the transparency log. Issuer/Subject uses a strict match, while IssuerRegExp and SubjectRegExp apply a regexp for matching.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| issuer | Issuer defines the issuer for this identity. | string | false |
| subject | Subject defines the subject for this identity. | string | false |
| issuerRegExp | IssuerRegExp specifies a regular expression to match the issuer for this identity. | string | false |
| subjectRegExp | SubjectRegExp specifies a regular expression to match the subject for this identity. | string | false |

[Back to TOC](#table-of-contents)

## ImagePattern

ImagePattern defines a pattern and its associated authorties If multiple patterns match a particular image, then ALL of those authorities must be satisfied for the image to be admitted.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| glob | Glob defines a globbing pattern. | string | true |

[Back to TOC](#table-of-contents)

## KeyRef

This references a public verification key stored in a secret in the cosign-system namespace. A KeyRef must specify only one of SecretRef, Data or KMS

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| secretRef | SecretRef sets a reference to a secret with the key. | [v1.SecretReference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#secretreference-v1-core) | false |
| data | Data contains the inline public key. | string | false |
| kms | KMS contains the KMS url of the public key Supported formats differ based on the KMS system used. | string | false |
| hashAlgorithm | HashAlgorithm always defaults to sha256 if the algorithm hasn't been explicitly set | string | false |

[Back to TOC](#table-of-contents)

## KeylessRef

KeylessRef contains location of the validating certificate and the identities against which to verify. KeylessRef will contain either the URL to the verifying certificate, or it will contain the certificate data inline or in a secret.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| url | URL defines a url to the keyless instance. | apis.URL | false |
| identities | Identities sets a list of identities. | [][Identity](#identity) | false |
| ca-cert | CACert sets a reference to CA certificate | [KeyRef](#keyref) | false |

[Back to TOC](#table-of-contents)

## MatchResource

MatchResource allows selecting resources based on its version, group and resource. It is also possible to select resources based on a list of matching labels.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| selector |  | [metav1.LabelSelector](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#labelselector-v1-meta) | false |

[Back to TOC](#table-of-contents)

## Policy

Policy specifies a policy to use for Attestation validation. Exactly one of Data, URL, or ConfigMapReference must be specified.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| type | Which kind of policy this is, currently only rego or cue are supported. Furthermore, only cue is tested :) | string | true |
| data | Data contains the policy definition. | string | false |
| url | URL to the policy data. | apis.URL | false |
| configMapRef | ConfigMapRef defines the reference to a configMap with the policy definition. | [ConfigMapReference](#configmapreference) | false |

[Back to TOC](#table-of-contents)

## Source

Source specifies the location of the signature

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| oci | OCI defines the registry from where to pull the signatures. | string | false |
| signaturePullSecrets | SignaturePullSecrets is an optional list of references to secrets in the same namespace as the deploying resource for pulling any of the signatures used by this Source. | [][v1.LocalObjectReference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.25/#localobjectreference-v1-core) | false |

[Back to TOC](#table-of-contents)

## StaticRef

StaticRef specifies that signatures / attestations are not validated but instead a static policy is applied against matching images.

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| action | Action defines how to handle a matching policy. | string | true |

[Back to TOC](#table-of-contents)

## TLog

TLog specifies the URL to a transparency log that holds the signature and public key information

| Field | Description | Scheme | Required |
| ----- | ----------- | ------ | -------- |
| url | URL sets the url to the rekor instance (by default the public rekor.sigstore.dev) | apis.URL | false |

[Back to TOC](#table-of-contents)