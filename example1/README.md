# Example 1

A component handling internal logic all in one spot. We want to show an Alert that can be dismissed, and we want to make sure it's stored in localstorage (the `hasEnvHelp` & `disableEnvHelp` functions below).

This seems pretty straight forward... this is a section on a page, so we have already broken it down a notch; why go further?

### [Declarative Design Example](./DECLARATIVE.md)

## Sample Code

```typescript jsx
import * as React from 'react';
import { Alert, AlertActionCloseButton, Button, Stack, StackItem, Title } from '@patternfly/react-core';
import { handleAddEnvironment } from './actions';
import { hasEnvHelp, disableEnvHelp } from './localStorage';
import EnvironmentCards from './EnvironmentCards';

const EnvironmentsSection: React.FC = () => {
  const [hideHelp, setHideInfo] = React.useState(hasEnvHelp());

  const disableHelp = () => {
    setHideInfo(false);
    disableEnvHelp();
  };

  const environmentHelp = (
    <Alert
      title="About environments"
      isInline
      variant="info"
      actionClose={<AlertActionCloseButton onClose={disableHelp} />}
      actionLinks={
        <Button variant="secondary" onClick={disableHelp}>
          Got it, thanks!
        </Button>
      }
    >
      Manage your continuous delivery process for your applications via environments. What&apos;s an
      &ldquo;environment&rdquo;? It&apos;s an abstraction of infrastructure. With environments,
      define a continuous delivery order and manage application components between them.
    </Alert>
  );

  return (
    <Stack hasGutter>
      <StackItem>
        <Title headingLevel="h2">Environments</Title>
      </StackItem>
      {!hideHelp && <StackItem>{environmentHelp}</StackItem>}
      <StackItem>
        <Button variant="primary" onClick={() => handleAddEnvironment()}>
          Create environment
        </Button>
      </StackItem>
      <StackItem>
        <EnvironmentCards />
      </StackItem>
    </Stack>
  );
};

export default EnvironmentsSection;
```
