# Transaction Types

TRON supports various types of transactions, categorized by their functions:

### Basic Transactions
- **Transfer Contract**: Transfer TRX between accounts
- **Transfer Asset Contract**: Transfer TRC10 tokens
- **Exchange Transaction Contract**: Trading on DEX

### Account Operations
- **Account Create Contract**: Create new accounts
- **Account Update Contract**: Update account name
- **Account Permission Update Contract**: Modify account permissions
- **Set Account ID Contract**: Set account ID

### Smart Contract Operations
- **Create Smart Contract**: Deploy new contracts
- **Trigger Smart Contract**: Call contract methods
- **Clear ABI Contract**: Clear contract ABI
- **Update Setting Contract**: Update contract settings
- **Update Energy Limit Contract**: Modify contract energy limit

### Resource Management
- **Freeze Balance Contract**: Freeze TRX for resources
- **Unfreeze Balance Contract**: Unfreeze TRX
- **Withdraw Balance Contract**: Withdraw rewards

### Network Governance
- **Vote Witness Contract**: Vote for Super Representatives
- **Witness Create/Update Contract**: SR operations
- **Proposal Create Contract**: Create network parameter proposals
- **Proposal Approve/Delete Contract**: Vote on proposals
- **Committee Update Contract**: Update committee settings

### Asset Operations
- **Asset Issue Contract**: Create TRC10 tokens
- **Update Asset Contract**: Update token parameters
- **Participate Asset Issue Contract**: Participate in token offerings
- **Unfreeze Asset Contract**: Unfreeze tokens

!!! note
    This is not an exhaustive list. For complete transaction types, please refer to the [Protocol](https://github.com/tronprotocol/protocol/blob/master/core/contract/common.proto).
