# Example 1

Declaring the entirety of environment help into its own hook, we have simplified this page down to a very simple rendering. When reading code you didn't write (or wrote a long time ago) this type of breakdown allows the reader to rationalize out the page extremely quickly.

`useEnvironmentHelpAlert` will either return a component, or not. The internal managing of onClicks, talking to localStorage, etc... this is all simplified down to a single call -- as far as the section is concerned.

## Our Original Component

```typescript jsx
import * as React from 'react';
import { Button, Stack, StackItem, Title } from '@patternfly/react-core';
import { handleAddEnvironment } from './actions';
import EnvironmentCards from './EnvironmentCards';
import useEnvironmentHelpAlert from './useEnvironmentHelpAlert';

const EnvironmentsSection: React.FC = () => {
  const environmentHelp = useEnvironmentHelpAlert();

  return (
    <Stack hasGutter>
      <StackItem>
        <Title headingLevel="h2">Environments</Title>
      </StackItem>
      {environmentHelp && <StackItem>{environmentHelp}</StackItem>}
      <StackItem>
        {/* eslint-disable-next-line no-alert */}
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

## Broken out "environmentHelp" Alert

A quick look behind the curtain, we have all the same logic we had before but it's completely isolated and made simple.

```typescript jsx
import * as React from 'react';
import { Alert, AlertActionCloseButton, Button } from '@patternfly/react-core';

const hideEnvHelpKey = 'hacDev/hide-env-help';

const hasEnvHelp = (): boolean => {
  return localStorage.getItem(hideEnvHelpKey) === 'true';
};

const disableEnvHelp = (): void => {
  localStorage.setItem(hideEnvHelpKey, 'true');
};

const useEnvironmentHelpAlert = (): React.ReactNode | null => {
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

  return hideHelp ? null : environmentHelp;
};

export default useEnvironmentHelpAlert;
```
