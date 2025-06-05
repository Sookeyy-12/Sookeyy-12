# Vault Processing Workflow Documentation

## Overview
This document describes a comprehensive vault processing system that handles financial instrument maturity, MT599 message processing, and automated status management through integration with ICSD (International Central Securities Depository) systems.

## System Components

### Core Systems
- **ICSD**: International Central Securities Depository system
- **GDI**: Global Data Integration system  
- **Vault Processing System**: Main workflow orchestrator
- **MT599 Messaging**: SWIFT message format for free format messages

### Key Identifiers
- **POMDG6 Series**: Various process identifiers (48330, 48532, 48467, 48678, etc.)
- **Certificate IDs**: Used for tracking and confirmation processes

## Detailed Workflow Process

### Phase 1: Initial Processing Setup

#### 1.1 Process Initiation
- **Trigger**: Start when Maturity Process is complete
- **Status Check**: Verify maturity date, process status, and instrument details
- **Condition**: Status = "Matured" AND certificates are in INT Vault

#### 1.2 MT599 Message Dispatch
- **Action**: Send MT599 to Common Depository on Maturity
- **Purpose**: Notify external systems of maturity completion
- **Integration Point**: ICSD communication layer

### Phase 2: Core Decision Logic

#### 2.1 Primary Status Verification
```
Decision Point: Maturity Status Check
- Input: Maturity date, Status, Certificate location
- Condition: Today ≥ Y*, Status = "Matured", Certificates in INT Vault
- True Path: Proceed to ICSD notification
- False Path: Set status to "Pending Disposal"
```

#### 2.2 ICSD Response Handling
```
Decision Point: MT599 Response from ICSD
- Wait Period: Monitor for ICSD response for maturity
- Response Types:
  - Received: Continue to disposal process
  - Not Received: Escalate or retry logic
```

### Phase 3: Disposal and Confirmation Process

#### 3.1 Task Creation and Assignment
- **Action**: Create a task to destroy the Global Notes
- **Assignment**: Route to appropriate disposal team
- **Tracking**: Generate task ID for audit trail

#### 3.2 Automated Status Management
- **GDI Integration**: Automatically set status as "Ready for Disposal"
- **Timestamp**: Record disposal readiness time
- **Notification**: Alert relevant stakeholders

#### 3.3 User Confirmation Loop
- **Task Assignment**: Back to confirm and back the Global Notes
- **User Action Required**: Manual verification and confirmation
- **Status Update**: User manually updates status upon completion

### Phase 4: Response Processing

#### 4.1 ICSD Communication
- **Outbound**: User prefills to send back the Global Notes and confirm in the Task
- **Integration**: Direct communication with ICSD systems
- **Response Handling**: Process ICSD acknowledgments

#### 4.2 Certificate Management
- **Action**: User regenerates the Certificate as csv and send to issuer
- **Format**: CSV export functionality
- **Distribution**: Automated sending to designated issuer contacts

### Phase 5: Completion and Cleanup

#### 5.1 Final Status Updates
- **GDI Integration**: Automatically set status as "Disposal Completed"
- **Audit Trail**: Complete transaction logging
- **Archive**: Move completed records to historical storage

#### 5.2 Email Notifications
- **Trigger**: Check emails for Single/Bulk ISIN or Pooled factor updates
- **Frequency**: Every 30 days or 3 weeks of Maturity
- **Content**: Status updates and process completion notifications

#### 5.3 Missing MT599 Handling
- **Monitor**: Create a view for Missing MT599
- **Alert System**: Identify and flag missing message responses
- **Resolution**: Manual intervention process for missing confirmations

## Integration Points

### ICSD Integration
- **Protocol**: SWIFT messaging (MT599)
- **Direction**: Bidirectional communication
- **Error Handling**: Retry logic and escalation procedures
- **Response Timeout**: Configurable waiting periods

### GDI Integration
- **Purpose**: Automated status management
- **Update Types**: 
  - "Pending Disposal"
  - "Ready for Disposal" 
  - "Disposal Completed"
- **Real-time**: Immediate status propagation

## Process Identifiers and References

### POMDG6 Series Mappings
- **POMDG6-48330**: Initial maturity process reference
- **POMDG6-48532**: Secondary process validation
- **POMDG6-48467**: ICSD response handling
- **POMDG6-48678**: Disposal task creation
- **POMDG6-48540**: Certificate regeneration
- **POMDG6-48515**: Final completion status
- **POMDG6-48507**: User confirmation process
- **POMDG6-48647**: Email notification system
- **POMDG6-48648**: Archive and cleanup

## Error Handling and Exceptions

### Missing Response Scenarios
- **MT599 Not Received**: Create monitoring view and alert system
- **ICSD Communication Failure**: Retry mechanism with exponential backoff
- **User Action Timeout**: Escalation to supervisory level

### Data Validation
- **Certificate Verification**: Validate certificate presence in INT Vault
- **Date Validation**: Confirm maturity dates and processing windows
- **Status Consistency**: Ensure status alignment across all systems

## Security and Compliance

### Audit Requirements
- **Transaction Logging**: Complete audit trail for all process steps
- **User Actions**: Log all manual interventions and confirmations
- **System Integration**: Track all ICSD and GDI communications

### Access Control
- **Role-Based**: Different access levels for different process stages
- **Approval Workflows**: Multi-level approval for critical actions
- **Segregation of Duties**: Separate roles for disposal and confirmation

## Performance Considerations

### Batch Processing
- **Bulk Operations**: Handle multiple ISINs in single workflow
- **Pooled Factors**: Special handling for pooled securities
- **Optimization**: Minimize system resource usage during peak periods

### Monitoring and Alerts
- **Real-time Status**: Dashboard for workflow progress monitoring
- **Exception Alerts**: Immediate notification of process failures
- **Performance Metrics**: Track processing times and success rates

## Implementation Notes for Developers

### Key Technical Requirements
1. **Message Queue Integration**: Implement robust MT599 message handling
2. **Database Transactions**: Ensure ACID compliance for status updates
3. **API Integration**: RESTful or SOAP interfaces for ICSD/GDI communication
4. **Error Recovery**: Implement circuit breaker patterns for external system failures
5. **Concurrency Control**: Handle multiple simultaneous vault processing requests

### Configuration Parameters
- **Timeout Values**: Configurable wait times for external system responses
- **Retry Logic**: Adjustable retry counts and intervals
- **Email Schedules**: Customizable notification frequencies
- **User Role Mappings**: Flexible assignment of process responsibilities

This workflow represents a comprehensive financial instrument lifecycle management system with robust integration capabilities and automated status management.