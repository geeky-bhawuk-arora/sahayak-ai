# Implementation Plan: Sahayak AI

## Overview

This implementation plan covers the complete development of Sahayak AI, a voice-first multilingual AI assistant for government services. The system is built entirely on AWS services using Python 3.11 for Lambda functions. The implementation follows a modular approach, building core infrastructure first, then implementing individual AWS service integrations, and finally wiring everything together with comprehensive testing.

## Tasks

- [ ] 1. Set up project structure and AWS infrastructure
  - Create Python project with virtual environment
  - Set up AWS CDK or CloudFormation templates for infrastructure
  - Configure DynamoDB tables (schemes, sessions, applications)
  - Set up S3 buckets for audio storage and mock data
  - Configure API Gateway with CORS and endpoints
  - Set up CloudWatch log groups and alarms
  - Create IAM roles and policies for Lambda
  - _Requirements: 13.1, 13.2, 13.3, 15.1, 15.2, 17.1, 17.2, 19.1, 19.2_

- [ ] 2. Implement data models and mock data
  - [ ] 2.1 Create Python data classes for core models
    - Implement Session, Turn, Context, Demographics classes
    - Implement Scheme, Criterion, Step, Document, ContactInfo classes
    - Implement Application and ApplicationStatus classes
    - Add serialization methods (to_dynamodb_item, from_dynamodb_item)
    - _Requirements: 3.4, 12.1, 12.4_
  
  - [ ] 2.2 Create mock government scheme data
    - Create JSON files for 5-6 government schemes (ration card, pension, PM-KISAN, Ayushman Bharat, Ujjwala, scholarship)
    - Include complete eligibility criteria, procedures, documents, and contact info
    - Add Hindi and English content for all fields
    - Upload mock data to S3 and populate DynamoDB tables
    - _Requirements: 3.1, 3.4, 12.1, 12.2, 12.4_
  
  - [ ] 2.3 Create mock application status data
    - Generate mock application records with various statuses
    - Include realistic reference numbers and timelines
    - Populate DynamoDB applications table
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 12.2, 12.3_

- [ ] 3. Implement DynamoDB operations module
  - [ ] 3.1 Create database client wrapper
    - Initialize boto3 DynamoDB client and resources
    - Implement connection pooling and error handling
    - Add retry logic with exponential backoff
    - _Requirements: 3.2, 15.2, 17.4_
  
  - [ ] 3.2 Implement scheme retrieval functions
    - Implement get_scheme_by_id(scheme_id) function
    - Implement get_all_schemes() function
    - Implement find_schemes_by_category(category) function
    - Add caching for frequently accessed schemes
    - _Requirements: 3.2, 3.3_
  
  - [ ]* 3.3 Write property test for scheme retrieval
    - **Property 6: Scheme Information Retrieval**
    - **Validates: Requirements 3.2**
  
  - [ ]* 3.4 Write property test for scheme data completeness
    - **Property 7: Scheme Data Completeness**
    - **Validates: Requirements 3.4**
  
  - [ ] 3.5 Implement eligibility assessment functions
    - Implement check_eligibility(scheme, demographics) function
    - Implement find_eligible_schemes(demographics) function
    - Add eligibility reasoning generation
    - _Requirements: 4.1, 4.2, 4.4_
  
  - [ ]* 3.6 Write property tests for eligibility assessment
    - **Property 8: Eligibility Evaluation Completeness**
    - **Property 9: Eligible Schemes Returned**
    - **Property 10: Eligibility Explanation Provided**
    - **Validates: Requirements 4.1, 4.2, 4.4**
  
  - [ ] 3.7 Implement session management functions
    - Implement create_session(language) function
    - Implement get_conversation_context(session_id) function
    - Implement update_conversation_context(session_id, turn_data) function
    - Add session expiration with TTL
    - _Requirements: 11.1, 11.2, 11.5_
  
  - [ ]* 3.8 Write property tests for context management
    - **Property 18: Conversation Context Preservation**
    - **Property 19: Reference Resolution**
    - **Property 20: Topic Switching Support**
    - **Property 21: Context Preservation During Errors**
    - **Validates: Requirements 11.1, 11.2, 11.4, 9.5**
  
  - [ ] 3.9 Implement application status functions
    - Implement get_application_status(application_ref) function
    - Add error handling for missing applications
    - _Requirements: 6.1, 6.5_
  
  - [ ]* 3.10 Write property test for status retrieval
    - **Property 14: Status Retrieval**
    - **Validates: Requirements 6.1**
  
  - [ ]* 3.11 Write unit tests for DynamoDB operations
    - Test error handling for missing schemes
    - Test error handling for database unavailability
    - Test session timeout behavior
    - Test eligibility edge cases
    - _Requirements: 3.3, 9.3, 17.2_
  
  - [ ]* 3.12 Write property test for database latency
    - **Property 23: Database Query Latency**
    - **Validates: Requirements 13.2**

- [ ] 4. Checkpoint - Verify data layer functionality
  - Ensure all DynamoDB operations work correctly
  - Verify mock data is properly loaded
  - Ensure all tests pass
  - Ask the user if questions arise

- [ ] 5. Implement Amazon Transcribe integration
  - [ ] 5.1 Create Transcribe client module
    - Initialize boto3 Transcribe client
    - Implement audio upload to S3 helper
    - Configure language settings (hi-IN, en-IN)
    - Add custom vocabulary for government scheme terms
    - _Requirements: 1.1, 1.4_
  
  - [ ] 5.2 Implement transcription function
    - Implement transcribe_audio(audio_data, language_hint) function
    - Handle both batch and streaming transcription
    - Add polling logic for job completion
    - Extract transcription text and confidence scores
    - Implement language detection
    - _Requirements: 1.1, 1.4_
  
  - [ ] 5.3 Implement Transcribe error handling
    - Handle NoSpeechDetectedException
    - Handle LowConfidenceException
    - Handle UnsupportedLanguageException
    - Handle timeout and service errors
    - Return user-friendly error messages in both languages
    - _Requirements: 1.2, 1.3, 9.1_
  
  - [ ]* 5.4 Write property test for transcription success
    - **Property 1: Transcription Success**
    - **Validates: Requirements 1.1**
  
  - [ ]* 5.5 Write property test for transcription latency
    - **Property 22: Transcription Latency**
    - **Validates: Requirements 13.1**
  
  - [ ]* 5.6 Write unit tests for Transcribe integration
    - Test Hindi audio transcription
    - Test English audio transcription
    - Test no speech error handling
    - Test low confidence error handling
    - Test language detection
    - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [ ] 6. Implement Amazon Bedrock integration
  - [ ] 6.1 Create Bedrock client module
    - Initialize boto3 Bedrock runtime client
    - Configure model settings (Claude 3 Sonnet)
    - Set up prompt templates (system, intent classification, response generation)
    - Add token usage tracking
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 7.1, 7.2, 7.3, 7.4, 7.5_
  
  - [ ] 6.2 Implement intent classification via Bedrock
    - Implement invoke_bedrock(user_text, language, context, schemes) function
    - Construct prompts with conversation history and scheme data
    - Parse JSON responses from Bedrock
    - Extract intent, entities, and confidence scores
    - Handle multi-intent queries
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5_
  
  - [ ]* 6.3 Write property tests for intent classification
    - **Property 2: Eligibility Intent Recognition**
    - **Property 3: Procedure Intent Recognition**
    - **Property 4: Document Intent Recognition**
    - **Property 5: Status Intent Recognition**
    - **Validates: Requirements 2.1, 2.2, 2.3, 2.4**
  
  - [ ] 6.4 Implement response generation via Bedrock
    - Enhance invoke_bedrock to generate natural language responses
    - Incorporate scheme data into responses
    - Ensure simple language appropriate for low-literacy users
    - Add follow-up question suggestions
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 10.1, 10.3, 18.1, 18.2, 18.3, 18.4_
  
  - [ ]* 6.5 Write property test for language consistency
    - **Property 15: Response Language Matches Input**
    - **Validates: Requirements 7.1, 7.2**
  
  - [ ] 6.5 Implement fallback rule-based classifier
    - Implement fallback_intent_classifier(text, language) function
    - Use keyword matching for common intents
    - Implement fallback_response_generator(intent, entities, language) function
    - Use template-based responses
    - _Requirements: 2.5, 9.2, 17.2_
  
  - [ ] 6.6 Implement Bedrock error handling
    - Handle ThrottlingException with retry logic
    - Handle ModelNotFoundException with fallback
    - Handle ValidationException
    - Handle timeout errors
    - Log all fallback usage
    - _Requirements: 9.2, 9.4, 17.2_
  
  - [ ]* 6.7 Write unit tests for Bedrock integration
    - Test intent classification for various queries
    - Test response generation in Hindi and English
    - Test fallback classifier activation
    - Test error handling and retries
    - Test low confidence handling
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 7.1, 7.2, 9.2_

- [ ] 7. Implement Amazon Polly integration
  - [ ] 7.1 Create Polly client module
    - Initialize boto3 Polly client
    - Configure voice settings (Aditi for Hindi, Raveena for English)
    - Set up neural TTS engine
    - Configure speech rate and audio format
    - _Requirements: 8.1, 8.2, 8.3, 14.2_
  
  - [ ] 7.2 Implement text-to-speech function
    - Implement synthesize_speech(text, language) function
    - Select appropriate voice based on language
    - Handle long text by chunking
    - Return audio stream as bytes
    - _Requirements: 8.1, 8.2, 8.3, 8.4_
  
  - [ ]* 7.3 Write property tests for speech synthesis
    - **Property 16: Speech Synthesis Language Consistency**
    - **Property 17: Text-to-Speech Conversion**
    - **Validates: Requirements 8.1, 8.2**
  
  - [ ]* 7.4 Write property test for synthesis latency
    - **Property 24: Speech Synthesis Latency**
    - **Validates: Requirements 13.3**
  
  - [ ] 7.5 Implement SSML enhancement (optional)
    - Implement add_ssml_markup(text, language) function
    - Add pauses at sentence boundaries
    - Emphasize important terms
    - Implement synthesize_speech_with_ssml function
    - _Requirements: 8.4_
  
  - [ ] 7.6 Implement Polly error handling
    - Handle TextLengthExceededException with chunking
    - Handle InvalidSsmlException with plain text retry
    - Handle service unavailability
    - Return text-only response as fallback
    - _Requirements: 9.4, 17.2_
  
  - [ ]* 7.7 Write unit tests for Polly integration
    - Test Hindi voice synthesis
    - Test English voice synthesis
    - Test long text chunking
    - Test SSML markup
    - Test error handling
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [ ] 8. Checkpoint - Verify AWS service integrations
  - Ensure Transcribe, Bedrock, and Polly integrations work independently
  - Verify error handling for each service
  - Ensure all tests pass
  - Ask the user if questions arise

- [ ] 9. Implement error handling framework
  - [ ] 9.1 Create ErrorHandler class
    - Implement centralized error handling logic
    - Add error categorization by component
    - Implement error recovery strategies
    - Generate user-friendly error messages in both languages
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5_
  
  - [ ] 9.2 Implement retry logic with exponential backoff
    - Implement retry_with_backoff(func, max_retries, base_delay) function
    - Add configurable retry parameters
    - Log retry attempts
    - _Requirements: 9.1, 9.4, 17.4_
  
  - [ ] 9.3 Implement circuit breaker pattern
    - Create CircuitBreaker class
    - Track failure counts and timeouts
    - Implement state transitions (CLOSED, OPEN, HALF_OPEN)
    - Apply to external service calls
    - _Requirements: 17.2, 17.4_
  
  - [ ] 9.4 Implement CloudWatch logging
    - Implement log_error_with_context(error, context) function
    - Add structured logging with session IDs and error details
    - Configure log levels
    - Set up error metrics
    - _Requirements: 19.1, 19.2, 19.5_
  
  - [ ]* 9.5 Write unit tests for error handling
    - Test each error type and recovery strategy
    - Test retry logic
    - Test circuit breaker state transitions
    - Test error message generation
    - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5_

- [ ] 10. Implement Lambda orchestrator function
  - [ ] 10.1 Create main Lambda handler
    - Implement lambda_handler(event, context) function
    - Parse API Gateway event and extract request data
    - Decode base64 audio data
    - Extract session ID and language hint
    - _Requirements: 1.1, 13.4_
  
  - [ ] 10.2 Implement end-to-end query processing flow
    - Call transcribe_audio to convert speech to text
    - Retrieve conversation context from DynamoDB
    - Call invoke_bedrock for intent and response
    - Query DynamoDB for scheme details based on intent
    - Enhance response with specific data
    - Call synthesize_speech to convert response to audio
    - Update conversation context in DynamoDB
    - _Requirements: 1.1, 2.1, 3.2, 4.1, 5.1, 6.1, 7.1, 8.1, 11.1_
  
  - [ ] 10.3 Implement response formatting
    - Format API Gateway response with audio and metadata
    - Include text transcript for accessibility
    - Add CORS headers
    - Return session ID and intent information
    - _Requirements: 8.1, 13.4_
  
  - [ ] 10.4 Implement system-level error handling
    - Wrap entire handler in try-catch
    - Handle Lambda timeout gracefully
    - Handle out-of-memory errors
    - Return user-friendly error responses
    - Log all errors to CloudWatch
    - _Requirements: 9.4, 17.2, 19.1_
  
  - [ ]* 10.5 Write property test for end-to-end latency
    - **Property 25: End-to-End Latency**
    - **Validates: Requirements 13.4**
  
  - [ ]* 10.6 Write integration tests for Lambda handler
    - Test complete flow from audio input to audio output
    - Test with Hindi queries
    - Test with English queries
    - Test multi-turn conversations
    - Test error scenarios
    - Mock AWS services using moto
    - _Requirements: 1.1, 2.1, 3.2, 7.1, 8.1, 11.1, 13.4_

- [ ] 11. Implement response enhancement logic
  - [ ] 11.1 Create response enhancer module
    - Implement enhance_response(bedrock_response, intent, entities, schemes) function
    - Add scheme-specific details based on intent
    - Format procedures as step-by-step instructions
    - Format document lists clearly
    - Add contact information
    - _Requirements: 5.1, 5.2, 5.3, 5.4_
  
  - [ ]* 11.2 Write property tests for response enhancement
    - **Property 11: Procedure Instructions Included**
    - **Property 12: Document List Completeness**
    - **Property 13: Contact Information Included**
    - **Validates: Requirements 5.1, 5.2, 5.3**
  
  - [ ]* 11.3 Write unit tests for response enhancement
    - Test procedure formatting
    - Test document list formatting
    - Test contact information inclusion
    - Test eligibility explanation formatting
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 12. Checkpoint - Verify Lambda orchestration
  - Ensure Lambda handler processes queries end-to-end
  - Verify all AWS services are properly orchestrated
  - Ensure error handling works across the entire flow
  - Ensure all tests pass
  - Ask the user if questions arise

- [ ] 13. Implement web UI frontend
  - [ ] 13.1 Create HTML interface
    - Create index.html with microphone button
    - Add recording indicator
    - Add audio playback controls
    - Add loading/processing indicators
    - Implement responsive design
    - _Requirements: 10.1, 10.2, 14.1, 14.5_
  
  - [ ] 13.2 Implement JavaScript audio capture
    - Use Web Audio API to capture microphone input
    - Implement startRecording() function
    - Implement stopRecording() function
    - Convert audio to base64 for transmission
    - Handle microphone permissions
    - _Requirements: 10.1, 14.1_
  
  - [ ] 13.3 Implement API communication
    - Implement sendAudioQuery(audioBlob, sessionId) function
    - Call API Gateway endpoint with audio data
    - Handle session ID generation and persistence
    - Parse response and extract audio
    - _Requirements: 13.4_
  
  - [ ] 13.4 Implement audio playback
    - Implement playAudioResponse(base64Audio) function
    - Decode base64 audio
    - Play audio through browser
    - Add playback controls
    - _Requirements: 8.1, 14.1_
  
  - [ ] 13.5 Implement error display
    - Show user-friendly error messages
    - Handle network errors
    - Handle microphone permission errors
    - Display retry options
    - _Requirements: 9.1, 9.2, 10.2_
  
  - [ ] 13.6 Add accessibility features
    - Implement keyboard navigation
    - Add ARIA labels for screen readers
    - Display text transcripts
    - Add visual feedback for all actions
    - _Requirements: 14.1, 14.5_
  
  - [ ]* 13.7 Test web UI functionality
    - Test microphone capture
    - Test audio playback
    - Test session management
    - Test error handling
    - Test on multiple browsers
    - _Requirements: 10.1, 10.2, 14.1_

- [ ] 14. Deploy infrastructure to AWS
  - [ ] 14.1 Configure AWS credentials and region
    - Set up AWS CLI with appropriate credentials
    - Configure default region
    - Verify IAM permissions
    - _Requirements: 15.1, 16.4_
  
  - [ ] 14.2 Deploy DynamoDB tables
    - Create schemes table with proper schema
    - Create sessions table with TTL enabled
    - Create applications table
    - Configure read/write capacity or on-demand billing
    - _Requirements: 3.1, 11.1, 15.1, 15.3_
  
  - [ ] 14.3 Deploy S3 buckets
    - Create audio-input bucket for Transcribe
    - Create data bucket for mock schemes
    - Create web-ui bucket for static hosting
    - Configure bucket policies and CORS
    - Enable versioning and encryption
    - _Requirements: 1.1, 12.1, 16.1_
  
  - [ ] 14.4 Upload mock data
    - Upload scheme JSON files to S3
    - Populate DynamoDB schemes table
    - Populate DynamoDB applications table
    - Verify data integrity
    - _Requirements: 3.1, 12.1, 12.2_
  
  - [ ] 14.5 Deploy Lambda function
    - Package Python code with dependencies
    - Create Lambda function with appropriate runtime (Python 3.11)
    - Configure environment variables
    - Set memory and timeout settings
    - Attach IAM role with required permissions
    - _Requirements: 13.4, 15.1, 16.1_
  
  - [ ] 14.6 Deploy API Gateway
    - Create REST API
    - Configure /api/query POST endpoint
    - Configure /api/session/new POST endpoint
    - Configure /api/health GET endpoint
    - Enable CORS
    - Set up request validation
    - Configure throttling limits
    - _Requirements: 13.4, 15.1_
  
  - [ ] 14.7 Deploy web UI to S3 + CloudFront
    - Upload HTML/JS/CSS files to S3
    - Configure S3 static website hosting
    - Create CloudFront distribution
    - Configure custom domain (optional)
    - Enable HTTPS
    - _Requirements: 14.1, 16.1_
  
  - [ ] 14.8 Configure CloudWatch monitoring
    - Set up log groups for Lambda
    - Create CloudWatch dashboard
    - Configure alarms for error rates
    - Configure alarms for latency
    - Set up SNS notifications for alerts
    - _Requirements: 13.5, 19.1, 19.2, 19.3, 19.4, 19.5_
  
  - [ ]* 14.9 Verify deployment
    - Test API Gateway endpoints
    - Test Lambda function invocation
    - Test web UI access
    - Verify CloudWatch logs
    - Test end-to-end flow in deployed environment
    - _Requirements: 17.1, 17.2_

- [ ] 15. Checkpoint - Verify deployment
  - Ensure all AWS resources are properly deployed
  - Verify web UI is accessible
  - Test end-to-end functionality in AWS environment
  - Ask the user if questions arise

- [ ] 16. Implement monitoring and analytics
  - [ ] 16.1 Create CloudWatch metrics
    - Track request count by intent
    - Track error rate by component
    - Track latency percentiles (p50, p95, p99)
    - Track Bedrock token usage
    - Track fallback usage rate
    - _Requirements: 19.1, 19.3, 19.6_
  
  - [ ] 16.2 Implement structured logging
    - Log all user interactions with anonymized IDs
    - Log intent classification results
    - Log scheme queries
    - Log error details with stack traces
    - Log performance metrics
    - _Requirements: 19.1, 19.2_
  
  - [ ] 16.3 Create CloudWatch dashboard
    - Add widgets for request volume
    - Add widgets for error rates
    - Add widgets for latency metrics
    - Add widgets for AWS service health
    - Add widgets for cost tracking
    - _Requirements: 19.3, 19.4_
  
  - [ ] 16.4 Configure alarms
    - Alarm for error rate > 5%
    - Alarm for latency > 10 seconds
    - Alarm for Lambda errors
    - Alarm for DynamoDB throttling
    - Alarm for Bedrock throttling
    - _Requirements: 19.5_
  
  - [ ]* 16.5 Test monitoring and alerting
    - Trigger test errors and verify alarms
    - Verify metrics are being collected
    - Verify dashboard displays correctly
    - Test SNS notifications
    - _Requirements: 19.1, 19.3, 19.5_

- [ ] 17. Implement performance optimizations
  - [ ] 17.1 Add caching layer
    - Implement in-memory cache for frequently accessed schemes
    - Cache Bedrock responses for common queries
    - Set appropriate TTL values
    - _Requirements: 13.2, 15.1_
  
  - [ ] 17.2 Optimize Lambda cold starts
    - Minimize package size
    - Use Lambda layers for dependencies
    - Implement connection pooling for AWS clients
    - Configure provisioned concurrency (optional)
    - _Requirements: 13.1, 13.4, 15.1_
  
  - [ ] 17.3 Optimize DynamoDB queries
    - Add indexes for common query patterns
    - Use batch operations where possible
    - Implement query result pagination
    - _Requirements: 13.2, 15.3_
  
  - [ ]* 17.4 Run performance tests
    - Test concurrent request handling (100 users)
    - Measure end-to-end latency under load
    - Verify 95th percentile latency < 5 seconds
    - Test auto-scaling behavior
    - _Requirements: 13.4, 15.1, 15.2_

- [ ] 18. Final integration testing and validation
  - [ ]* 18.1 Run complete test suite
    - Run all unit tests
    - Run all property-based tests
    - Run all integration tests
    - Verify >80% code coverage
    - _Requirements: All_
  
  - [ ]* 18.2 Test all user scenarios
    - Test eligibility check flow
    - Test scheme information flow
    - Test application procedure flow
    - Test document inquiry flow
    - Test application status check flow
    - Test multi-turn conversations
    - Test error recovery
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 4.1, 5.1, 5.2, 6.1, 11.1_
  
  - [ ]* 18.3 Test bilingual functionality
    - Test Hindi voice input and output
    - Test English voice input and output
    - Test language switching
    - Test mixed language queries
    - _Requirements: 1.1, 1.4, 7.1, 7.2, 8.2, 18.1, 18.3_
  
  - [ ]* 18.4 Test error handling scenarios
    - Test no speech detected
    - Test unclear speech
    - Test unsupported language
    - Test scheme not found
    - Test application not found
    - Test service unavailability
    - Test network errors
    - _Requirements: 1.2, 1.3, 9.1, 9.2, 9.3, 9.4_
  
  - [ ]* 18.5 Validate performance requirements
    - Verify transcription latency < 500ms
    - Verify database query latency < 1s
    - Verify synthesis latency < 500ms
    - Verify end-to-end latency < 5s for 95% of requests
    - _Requirements: 13.1, 13.2, 13.3, 13.4_
  
  - [ ]* 18.6 Validate accessibility requirements
    - Test voice-only interaction
    - Test with screen readers
    - Verify simple language usage
    - Test audio cues and confirmations
    - _Requirements: 14.1, 14.2, 14.4, 14.5_

- [ ] 19. Final checkpoint - Production readiness
  - Ensure all tests pass
  - Verify all requirements are met
  - Confirm system is ready for demonstration
  - Ask the user if questions arise

## Notes

- Tasks marked with `*` are optional testing tasks and can be skipped for faster MVP delivery
- Each task references specific requirements for traceability
- Property tests validate universal correctness properties from the design document
- Unit tests validate specific examples and edge cases
- The implementation uses Python 3.11 for all Lambda functions
- All AWS services are configured for the MVP scope (100 concurrent users, 5-6 schemes)
- Mock data is used throughout - no real government API integration
- The system supports Hindi and English languages only
- Checkpoints ensure incremental validation and provide opportunities for user feedback
