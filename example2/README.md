# Example 2

Form pages are often very verbose in React. Using hooks usually ends up with one hook per field, with additional hooks for error states and any number of validation callbacks. Using Formik (as shown here) automatically reduces a lot of that down and breaks it apart to be more declarative while handling some boilerplate stuff.

This example showcases how you can take an already Formik powered form and make it more declarative and straight forward to consume.

### [Declarative Design Example](./DECLARATIVE.md)

## Code Sample

```typescript jsx
import * as React from 'react';
import { ExpandableSection, Form, SelectVariant, Spinner, Title } from '@patternfly/react-core';
import { FormikProps, useField } from 'formik';
import * as _ from 'lodash-es';
import { FormFooter, InputField, SelectInputField } from '../../../shared';
import useEnvironments from '../useEnvironments';
import { EnvironmentFormData, StatusObjectState } from './types';
import useClusters from './useClusters';
import { createClusterOptions, createOrderOptions } from './utils';

type EnvironmentFormContentProps = FormikProps<EnvironmentFormData> & {
  isEditMode: boolean;
};

const EnvironmentFormContent: React.FC<EnvironmentFormContentProps> = ({
  handleSubmit,
  handleReset,
  isSubmitting,
  errors,
  status: typelessStatus,
  setStatus,
  isEditMode,
}) => {
  /** Formik doesn't let us type status -- for debug purposes this is typed */
  const status: StatusObjectState = typelessStatus;
  const environments = useEnvironments();
  const [isExpanded, setExpanded] = React.useState(false);
  const clusters = useClusters();
  const [{ value }] = useField<EnvironmentFormData['cluster']>('cluster');
  const selectedCluster = clusters.find((cluster) => cluster.name === value);

  const isMissingClusterList = clusters.length === 0;
  const { stillLoadingClusters } = status;
  React.useEffect(() => {
    if (stillLoadingClusters !== isMissingClusterList) {
      const statusState = {
        stillLoadingClusters: isMissingClusterList,
      };
      setStatus(statusState);
    }
  }, [stillLoadingClusters, setStatus, isMissingClusterList]);

  return (
    <Form onSubmit={handleSubmit}>
      <Title headingLevel="h2">Define environment</Title>
      <p>This environment will be available only within this workspace.</p>
      <InputField name="name" label="Environment name" />
      <SelectInputField
        isDisabled
        variant={SelectVariant.single}
        options={[{ disabled: true, value: 'automatic', label: 'Automatic' }]}
        name="strategy"
        label="Deployment strategy"
        helpText="Apps will require manual trigger to move to this environment."
      />
      <SelectInputField
        isDisabled={environments.length === 1 && isEditMode}
        variant={SelectVariant.single}
        options={createOrderOptions(environments)}
        name="order"
        label="Order in continuous delivery"
        helpText="Set the default continuous delivery order for your application. You can modify this later for each application."
      />
      <Title headingLevel="h2">Select cluster</Title>
      <p>Currently, infrastructure for environments are supporting one cluster.</p>
      {selectedCluster ? (
        <>
          <SelectInputField
            variant={SelectVariant.single}
            isDisabled={clusters.length === 1}
            options={createClusterOptions(clusters)}
            name="cluster"
            label="Cluster"
          />
          <ExpandableSection
            toggleText={`${isExpanded ? 'View' : 'Hide'} cluster details`}
            onToggle={() => setExpanded(!isExpanded)}
            isExpanded={isExpanded}
          >
            Some content that UX will determine. Selected cluster: {selectedCluster.name}
          </ExpandableSection>
        </>
      ) : (
        <Spinner />
      )}
      <FormFooter
        submitLabel="Create environment"
        cancelLabel="Cancel"
        handleCancel={handleReset}
        isSubmitting={isSubmitting}
        errorMessage={status?.formError}
        disableSubmit={!_.isEmpty(status) || !_.isEmpty(errors)}
      />
    </Form>
  );
};

export default EnvironmentFormContent;
```
