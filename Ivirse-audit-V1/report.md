---
documentclass: article
fontsize: 12pt
header-includes: 
    \input{trufy-template.tex}
    \usepackage[top=3.5cm,bottom=2cm,left=3cm,right=2.5cm,footskip=0.25in]{geometry}
    \newcommand\boldred[1]{\textcolor{red}{\textbf{#1}}}
    \newcommand\boldamber[1]{\textcolor{myamber}{\textbf{#1}}}
    \newcommand\boldgreen[1]{\textcolor{mygreen}{\textbf{#1}}}
    \newcommand\boldblue[1]{\textcolor{blue}{\textbf{#1}}}
numbersections: true
output: 
    pdf_document:
        toc: true
        toc_depth: 2
---

\begin{titlepage}
    \begin{center}

        \includegraphics[width=0.6\textwidth]{trufy-logo.png}

        \vspace{1.5cm}
        \Huge
        \textbf{
            Smart Contract Audit Report \\
        }
        \Huge
        for \\
        \Huge
        \textbf{
            IVIRSE
        }

        \Large
        Preliminary Comments

        \vfill
        \textbf{September, 2022}
        \vspace{1.5cm}

    \end{center}
\end{titlepage}

\tableofcontents

\newpage

# Introduction

Trufy (Consultant) was contracted by IVIRSE (Customer) to conduct a Smart Contract Code Review and Security Analysis. This report presents the findings of the security assessment of the Customer's smart contract and its code review conducted between September 7th, 2022 -- September 14th, 2022.

## Project Summary

- Project Name: IVIRSE
- Language: Solidity
- Codebase: [https://github.com/IVIRSE/IVIRSE](https://github.com/IVIRSE/IVIRSE)
- Commit: fc22999e16afe7ae1581f95f8c9b358bc0dcc773
- Audit method: Static Analysis, Manual Review
- Scope:
  - contracts/community/CampaignManagement.sol

## Vulnerability Summary

| Severity                        | # of Findings |
| :------------------------------------------------------------ | :------------------------------------------------------------ |
| \boldred{Critical}       | 0             |
| \boldamber{Medium}     | 1             |
| \boldgreen{Low}        | 2             |
| \boldblue{Informational} | 11            |

\newpage

# Findings

| **ID**                                                                                           | **Title**                                                                                | **Type**         | **Severity**                    |
| :-------- | :-------------------------------------------- | :----------------------- | :--------------- |
| [ID-01](#id-01-suggested-modification-for-project-structure)                                              | Suggested modification for project structure                                                      | Coding Style     | \boldblue{Informational} |
| [ID-02](#id-02-lack-of-contract-information-in-the-error-message-of-require)                     | Lack of contract information in the error message of `require()`                         | Coding Style     | \boldblue{Informational} |
| [ID-03](#id-03-bad-modifier-naming)                                                              | Bad modifier naming                                                                      | Coding Style     | \boldblue{Informational} |
| [ID-04](#id-04-bad-gas-optimization-in-function-_validatetimesandamounts)                        | Bad gas optimization in function `_validateTimesAndAmounts()`                            | Gas Optimization | \boldblue{Informational} |
| [ID-05](#id-05-bad-gas-optimization-in-function-_validateaccountsandamounts)                     | Bad gas optimization in function `_validateAccountsAndAmounts()`                         | Gas Optimization | \boldblue{Informational} |
| [ID-06](#id-06-inconsistency-unit-of-parameter-amount)                                           | Inconsistency unit of parameter `amount`                                                 | Inconsistency    | \boldblue{Informational} |
| [ID-07](#id-07-bad-naming-convention)                                                            | Bad naming convention                                                                    | Coding Style     | \boldblue{Informational} |
| [ID-08](#id-08-bad-function-naming)                                                              | Bad function naming                                                                      | Coding Style     | \boldblue{Informational} |
| [ID-09](#id-09-bad-gas-optimization-in-function-_getusedtokenbystatus-and-_getallowancebystatus) | Bad gas optimization in function `_getUsedTokenByStatus()` and `_getAllowanceByStatus()` | Gas Optimization | \boldblue{Informational} |
| [ID-10](#id-10-logic-issue-of-function-_createcampaign)                                          | Logic issue of function `_createCampaign()`                                              | Logical Issue    | \boldgreen{Low}        |
| [ID-11](#id-11-bad-modifier-naming)                                                              | Bad modifier naming                                                                      | Coding Style     | \boldblue{Informational} |
| [ID-12](#id-12-function-_getallowancebystatus-calculate-wrong-activetoken)                       | Function `_getAllowanceByStatus()` calculate wrong `activeToken`                         | Logical Issue    | \boldamber{Medium}     |
| [ID-13](#id-13-unbalance-issue-of-campaign-creation-and-deletion)       | Unbalance issue of campaign creation and deletion             | Logical Issue    | \boldgreen{Low}        |
| [ID-14](#id-14-proper-usage-of-public-and-external-access)                                       | Proper usage of `public` and `external` access                                           | Gas optimization | \boldblue{Informational} |

\newpage

# Detailed Results

## ID-01: Suggested modification for project structure

| **Type**     | **Severity**  | **Location** |
| :----------- | :------------ | :----------- |
| Coding Style | \boldblue{Informational} |              |

### Description

All interfaces should be located in one directory.

### Recommendation

Recommended directory tree:

\includegraphics[width=0.5\textwidth]{directory-tree.pdf}

\newpage

## ID-02: Lack of contract information in the error message of `require()`

| **Type**     | **Severity**  | **Location**            |
| :----------- | :------------ | :---------------------- |
| Coding Style | \boldblue{Informational} | Lines 67, 81,  87, etc. |

### Description

It is better to include more details about the contract where the issue was thrown in the error message.

For example, in lines 67 -- 69:

\begin{lstlisting}[language=Solidity, firstnumber=67]
require(
    totalCampaignConsensus > adminsLength.div(2),
    "Not enough consensus!"
);
\end{lstlisting}

### Recommendation

Adding contract name to the error message:

\begin{lstlisting}[language=Solidity, firstnumber=67]
require(
    totalCampaignConsensus > adminsLength.div(2),
    "CampaignManagement: Not enough consensus!"
);
\end{lstlisting}

\newpage

## ID-03: Bad modifier naming

| **Type**     | **Severity**  | **Location** |
| :----------- | :------------ | :----------- |
| Coding Style | \boldblue{Informational} | Line 102     |

### Description

Modifier `isExist()` can be bypassed if `campaignName` has **not existed** yet and vice versa.

### Recommendation

It is better to change name of modifier `isExist()` to `ifNotExist()`.

\newpage

## ID-04: Bad gas optimization in function `_validateTimesAndAmounts()`

| **Type**         | **Severity**  | **Location** |
| :--------------- | :------------ | :----------- |
| Gas Optimization | \boldblue{Informational} | Line 256     |

### Description

The function `_validateTimesAndAmounts()` should require `numberOfTime == numberOfAmount` first, then require `numberOfTime > 0` instead of require `numberOfTime > 0 && numberOfAmount > 0`.

### Recommendation

\begin{lstlisting}[language=Solidity, firstnumber=256]
require(
    numberOfTime == numberOfAmount,
    "CampaignManagement: Times and accounts not match!"
);
require(
    numberOfTime > 0,
    "CampaignManagement: Times can't be zero!"
);
\end{lstlisting}

\newpage

## ID-05: Bad gas optimization in function `_validateAccountsAndAmounts()`

| **Type**         | **Severity**  | **Location** |
| :--------------- | :------------ | :----------- |
| Gas Optimization | \boldblue{Informational} | Line 278     |

### Description

The function `_validateAccountsAndAmounts()` should require `numberOfAccount == numberOfAmount` first, then require `numberOfAccount > 0`, instead of require `numberOfAccount > 0 && numberOfAmount > 0`.

### Recommendation

\begin{lstlisting}[language=Solidity, firstnumber=278]
require(
    numberOfAccount == numberOfAmount,
    "CampaignManagement: Amounts and times not match!"
);
require(
    numberOfAccount > 0,
    "CampaignManagement: Accounts can't be zero!"
);
\end{lstlisting}

\newpage

## ID-06: Inconsistency unit of parameter `amount`

| **Type**      | **Severity**  | **Location**          |
| :------------ | :------------ | :-------------------- |
| Inconsistency | \boldblue{Informational} | Lines 113,258,280,297 |

### Description

Parameters `amounts` represents the amount of tokens. In different functions, these parameters are inconsistent on unit.

For example, in the constructor, parameter `amounts_` is not multiplied by decimals ${10}^{18}$.  But in function  `_createCampaign()`, parameter `_amounts` is in the form of multiplied by decimals ${10}^{18}$.

This inconsistency may lead to confusion in programming.

### Recommendation

Parameter `amount` should be in the form of multiplied by decimals, in order to be able to represent the amount of tokens as a floating point number.

\newpage

## ID-07: Bad naming convention

| **Type**     | **Severity**  | **Location** |
| :----------- | :------------ | :----------- |
| Coding Style | \boldblue{Informational} |              |

### Description

In `constructor`:

\begin{lstlisting}[language=Solidity, firstnumber=113]
constructor(
    IERC20 token_,
    uint256[] memory times_,
    uint256[] memory amounts_
)
\end{lstlisting}

Parameter `times_` and `amounts_` have suffix `_`.

In function `_createCampaign()`:

\begin{lstlisting}[language=Solidity, firstnumber=294]
function _createCampaign(
string memory_campaignName,
address[] memory_accounts,
uint256[] memory_amounts,
    uint256 releaseTime,
bool_isUpdate
)
\end{lstlisting}

Parameter `_campaignName` has prefix `_`, but parameter `releaseTime` has no prefix `_`.

### Recommendation

Name of parameters passed into functions should be unified one of the two conventions:

- Neither add underscore character `_` to prefix or suffix of parameters.
- Either add underscore character `_` to prefix or suffix of parameters.

\newpage

## ID-08: Bad function naming

| **Type**     | **Severity**  | **Location**   |
| :----------- | :------------ | :------------- |
| Coding Style | \boldblue{Informational} | Lines 230, 391 |

### Description

Name of function `getTokenCanUse()` in line 230 should be changed to `getUseableTokenAmount`.

Name of function `_getTokenMustNotUsed()` in line 391 should be changed to \
`_getUnuseableTokenAmount`.

\newpage

## ID-09: Bad gas optimization in function `_getUsedTokenByStatus()` and `_getAllowanceByStatus()`

| **Type**         | **Severity**  | **Location**   |
| :--------------- | :------------ | :------------- |
| Gas Optimization | \boldblue{Informational} | Lines 403, 423 |

### Description

The declaration of parameters `participants` and `participantLength` may be redundant when `if condition` is not passed.

### Recommendation

The declaration of parameters `participants` and `participantLength` should be in the scope of `if` to optimize gas. These functions can be rewritten as follows:

\begin{lstlisting}[language=Solidity, firstnumber=403]
function _getUsedTokenByStatus(CampaignStatus status)
        private
        view
        returns (uint256 activeToken)
    {
        uint256 campaignLength =_campaignNames.length;
        for (uint256 i = 0; i < campaignLength; i++) {
            string memory name = _campaignNames[i];
            Campaign memory campaign =_campaigns[name];
            if (campaign.status == status) {
                Participant[] memory participants = campaign.participants;
                uint256 participantLength = participants.length;
                for (uint256 j = 0; j < participantLength; j++) {
                    Participant memory joiner = participants[j];
                    activeToken += joiner.amount;
                }
            }
        }
    }
\end{lstlisting}

\begin{lstlisting}[language=Solidity, firstnumber=423]
function _getAllowanceByStatus(CampaignStatus status)
        private
        view
        returns (uint256 activeToken)
    {
        uint256 campaignLength =_campaignNames.length;
        for (uint256 i = 0; i < campaignLength; i++) {
            string memory name = _campaignNames[i];
            Campaign memory campaign =_campaigns[name];
            if (campaign.status == status) {
                Participant[] memory participants = campaign.participants;
                uint256 participantLength = participants.length;
                for (uint256 j = 0; j < participantLength; j++) {
                    Participant memory joiner = participants[j];
                    activeToken += _token.allowance(
                        address(this),
                        joiner.account
                    );
                }
            }
        }
    }
\end{lstlisting}

\newpage

## ID-10: Logic issue of function `_createCampaign()`

| **Type**      | **Severity** | **Location** |
| :------------ | :----------- | :----------- |
| Logical Issue | \boldgreen{Low}          | Line 294     |

### Description

In line 302:

\begin{lstlisting}[language=Solidity, firstnumber=302]
_campaignNames.push(_campaignName);
\end{lstlisting}

Value `campaignName` is pushed into array `_campaignNames`. Therefore, function `_getTokenCanUse()` will iterate over this `campaignName` uselessly.

### Recommendation

Despite `campaignName` doesn’t have data in mapping `_campaigns`, this is not optimal for gas and may lead to some logical issues in the future. It is better to move line 302:

\begin{lstlisting}[language=Solidity, firstnumber=302]
_campaignNames.push(_campaignName);
\end{lstlisting}

right below line 309

\begin{lstlisting}[language=Solidity, firstnumber=309]
require(tokenCanUse >= totalAmount, "Not enough erc20 token");
\end{lstlisting}

\newpage

## ID-11: Bad modifier naming

| **Type**     | **Severity**  | **Location** |
| :----------- | :------------ | :----------- |
| Coding Style | \boldblue{Informational} | Line 87      |

### Description

`AdminConsentStatus` has three values: *NoAction*, *Reject*, *Accept*. It is more logical to change the name of modifer `confirmedRelease` to `notRejectedRelease`. It is compatible with modifer `notConfirmedRelease`.

\newpage

## ID-12: Function `_getAllowanceByStatus()` calculate wrong `activeToken`

| **Type**      | **Severity** | **Location** |
| :------------ | :----------- | :----------- |
| Logical Issue | \boldamber{Medium}       | Line 423     |

### Description

Function `_getAllowanceByStatus(status)` calculates the total allowance amount of all participants in all campaign that has `CampaignStatus` = `status`.

In function `_getAllowanceByStatus(status)` (line 423), `_token.allowance()` will return token amount which an *account* can spend over all campaigns. By the following code
\begin{lstlisting}[language=Solidity, firstnumber=437]
activeToken += _token.allowance(address(this), joiner.account);
\end{lstlisting}
if an *account* joins more than one campaign, his `activeToken` will be plus more than one time of `_token.allowance()`.

As a result, function `_getAllowanceByStatus(status)` does not perform as expected.

\newpage

## ID-13: Unbalance issue of campaign creation and deletion

| **Type**      | **Severity** | **Location** |
| :------------ | :----------- | :----------- |
| Logical Issue | \boldgreen{Low}          | Line 129     |

### Description

Any malicious admin can call function `createCampaign()` to create a useless campaign and decrease the amount of `tokenCanUse`. But to *Delete* these campaigns, we need at least half of the admins to vote *Reject* to *Delete* spam campaign and restore `tokenCanUse`.

\newpage

## ID-14: Proper usage of `public` and `external` access

| **Type**         | **Severity**  | **Location** |
| :--------------- | :------------ | :----------- |
| Gas Optimization | \boldblue{Informational} |              |

### Description

The public functions:

- `createCampaign()` in line 129
- `adminAcceptRelease()` in line 141
- `adminRejectRelease()` in line 153
- `release()` in line 165
- `deleteCampaign()` in line 195
- `getDatas()` in line 209
- `getCampaigns()` in line 213
- `getCampaign()` in line 217

are only called from external, it is better to change from `public` to `external` for gas optimization.

\newpage

# Appendix

## Severity Definitions

*\boldred{Critical}*

This level vulnerabilities could be exploited easily and can lead to asset loss, data loss, asset, or data manipulation. They should be fixed right away.

*\boldamber{Medium}*

This level vulnerabilities are hard to exploit but very important to fix, they carry an elevated risk of smart contract manipulation, which can lead to critical-risk severity.

*\boldgreen{Low}*

This level vulnerabilities should be fixed, as they carry an inherent risk of future exploits, and hacks which may or may not impact the smart contract execution.

*\boldblue{Informational}*

This level vulnerabilities can be ignored. They are code style violations and informational statements in the code. They may not affect the smart contract execution.

## Finding Categories

***Gas Optimization***

Gas Optimization findings refer to exhibits that do not affect the functionality of the code but generate different, more optimal EVM opcodes resulting in a reduction on the total gas cost of a transaction.

***Logical Issue***

Logical Issue findings are exhibits that detail a fault in the logic of the linked code, such as an incorrect notion on how block.timestamp works.

***Inconsistency***

Inconsistency findings refer to functions that should seemingly behave similarly yet contain different code, such as a constructor assignment imposing different require statements on the input variables than a setter function.

***Coding Style***

Coding Style findings usually do not affect the generated byte-code and comment on how to make the codebase more legible and as a result easily maintainable.

***Mathematical Operations***

Mathematical Operation exhibits entail findings that relate to mishandling of math formulas, such as overflows, incorrect operations etc.

***Dead Code***

Code that otherwise does not affect the functionality of the codebase and can be safely omitted.

***Language Specific***

Language Specific findings are issues that would only arise within Solidity, i.e. incorrect usage of `private` or `delete`.
