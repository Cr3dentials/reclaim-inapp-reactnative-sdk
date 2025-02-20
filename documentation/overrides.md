# ReclaimVerification Override Configuration

The `setOverrides` method allows you to customize various aspects of the Reclaim SDK's behavior. This documentation covers all available override options and their usage.

## Basic Usage

```typescript
const reclaimVerification = new ReclaimVerification();
reclaimVerification.setOverrides({
  // override configurations
});
```

For a complete example, see [App.overrides.tsx](https://github.com/reclaimprotocol/reclaim-inapp-reactnative-sdk/blob/main/example/src/App.overrides.tsx).

## Available Override Options

### Provider Configuration

Configure custom provider settings:

```typescript
provider: {
  // Custom provider JSON configuration
  // Useful for overriding default provider settings or testing with custom providers
  jsonString: await fetchProviderConfig()
}
```

or 

```typescript
provider: {
  // Custom provider JSON configuration
  // A url to a json file that contains the provider configuration
  url: 'https://api.example.org/some-api/providers/6d3f6753-7ee6-49ee-a545-62f1b1822ae5'
}
```

- The JSON string or url must point to a valid provider configuration that follows the [provider schema](schemas/provider.schema).
- Examples of provider configurations can be found in the `https://api.reclaimprotocol.org/api/providers/${provider_id}` http api. For example, [Github Username Provider](https://api.reclaimprotocol.org/api/providers/6d3f6753-7ee6-49ee-a545-62f1b1822ae5).

### Logging Configuration

Control SDK logging behavior:

```typescript
logConsumer: {
  // Disable/enable telemetry collection
  canSdkCollectTelemetry: false,
  
  // Disable/enable SDK console logging
  canSdkPrintLogs: false,
  
  // Custom log handler function
  onLogs: (logJsonString, _) => {
    console.log(logJsonString);
  }
}
```

### App Information

Customize the application's identity during claim creation:

```typescript
appInfo: {
  // Custom application name
  appName: "Your App Name",
  
  // Custom application logo/image
  appImageUrl: "https://your-app-image-url.com/image.png"
}
```

### Feature Options

Configure SDK behavior and features:

```typescript
featureOptions: {
  // Cookie persistence configuration
  cookiePersist: null,
  
  // Enable/disable single reclaim request mode
  singleReclaimRequest: false,
  
  // Idle time threshold (in seconds) for manual verification trigger  (Default from reclaimprotocol.org: `2`)
  idleTimeThresholdForManualVerificationTrigger: 2,
  
  // Session timeout (in seconds) for manual verification trigger (Default from reclaimprotocol.org: `180`)
  sessionTimeoutForManualVerificationTrigger: 180,
  
  // Custom attestor browser RPC URL (Default from reclaimprotocol.org: `https://attestor.reclaimprotocol.org/browser-rpc`)
  attestorBrowserRpcUrl: 'https://attestor.reclaimprotocol.org/browser-rpc',
  
  // Enable/disable response redaction regex escaping by the app (Default from reclaimprotocol.org: `false`)
  isResponseRedactionRegexEscapingEnabled: false,
  // Whether AI Flow can be enabled by the SDK. (Default from reclaimprotocol.org: `false`)
  isAIFlowEnabled: false,
}
```

Setting all values to non-null values will prevent the SDK from fetching feature settings from reclaim protocol APIs server.

### Session Management
Handle session-related events:
```typescript
sessionManagement: {
  // Session logging handler
  onLog: (event) => {
    console.log(event);
  },
  
  // Session creation request handler
  // Return true to allow session creation
  // Returning false will cancel the session with session expired exception.
  onSessionCreateRequest: async (event) => {
    console.log(event);
    return true;
  },
  
  // Session update request handler
  // Return true to allow session update
  // Returning false will cancel the session with session expired exception.
  onSessionUpdateRequest: async (event) => {
    console.log(event);
    return true;
  }
}
```

## Complete Example

Here's a full example showing all available override options:

```typescript
reclaimVerification.setOverrides({
  provider: {
    jsonString: await fetchProviderConfig()
  },
  logConsumer: {
    canSdkCollectTelemetry: false,
    canSdkPrintLogs: false,
    onLogs: (logJsonString, _) => {
      console.log({ "reclaim.logs": logJsonString });
    }
  },
  appInfo: {
    appName: "Custom App Name",
    appImageUrl: "https://placehold.co/400x400/png"
  },
  featureOptions: {
    cookiePersist: null,
    singleReclaimRequest: false,
    idleTimeThresholdForManualVerificationTrigger: 2,
    sessionTimeoutForManualVerificationTrigger: 180,
    attestorBrowserRpcUrl: 'https://attestor.reclaimprotocol.org/browser-rpc',
    isResponseRedactionRegexEscapingEnabled: false,
    isAIFlowEnabled: false
  },
  sessionManagement: {
    onLog: (event) => {
      console.log({ "reclaim.session.log": event });
    },
    onSessionCreateRequest: async (event) => {
      console.log({ "reclaim.session.createRequest": event });
      return true;
    },
    onSessionUpdateRequest: async (event) => {
      console.log({ "reclaim.session.updateRequest": event });
      return true;
    }
  }
});
```

## Best Practices

1. Set overrides before calling `startVerification`
2. Setting overrides again removes the previous overrides.
3. Handle all session management events appropriately.
4. Use appropriate timeout values for your use case
5. Document any custom provider configurations
6. Test thoroughly when modifying default behaviors

## Notes

- All override options are optional
- Overrides persist until explicitly changed or reset
- Some features may require specific override combinations to work properly
- Overriding all options will cause the app to not use *any* reclaim protocol APIs.
