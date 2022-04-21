# Example 2

As we immediately see below, the form is broken up into two sections. This was easily done as the mocks indicated separation with headers in the middle of the form -- this may be harder depending on the size and structure of a form but like-items should be considered their own separate section. (eg. to take an [OpenShift Console](https://github.com/openshift/console) concept, an application name & the grouping it may have on Topology).

## Top-Level Form Content

```typescript jsx
import * as React from 'react';
import { Form } from '@patternfly/react-core';
import { FormikProps } from 'formik';
import * as _ from 'lodash-es';
import { FormFooter } from '../../../shared';
import DefineEnvironmentFormSection from './DefineEnvironmentFormSection';
import SelectClusterFormSection from './SelectClusterFormSection';
import { EnvironmentFormData, StatusObjectState } from './types';

type EnvironmentFormContentProps = FormikProps<EnvironmentFormData> & {
  isEditMode: boolean;
};

const EnvironmentFormContent: React.FC<EnvironmentFormContentProps> = ({
  handleSubmit,
  handleReset,
  isSubmitting,
  errors,
  status: typelessStatus,
  isEditMode,
}) => {
  /** Formik doesn't let us type status -- for debug purposes this is typed */
  const status: StatusObjectState = typelessStatus;

  return (
    <Form onSubmit={handleSubmit}>
      <DefineEnvironmentFormSection isEditMode={isEditMode} />
      <SelectClusterFormSection />
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

## A quick look at the new sections

### DefineEnvironmentFormSection

```typescript jsx
import * as React from 'react';
import { SelectVariant, Title } from '@patternfly/react-core';
import { InputField, SelectInputField } from '../../../shared';
import useEnvironments from '../useEnvironments';
import { createOrderOptions } from './utils';

type DefineEnvironmentFormSectionProps = {
  isEditMode: boolean;
};

const DefineEnvironmentFormSection: React.FC<DefineEnvironmentFormSectionProps> = ({
  isEditMode,
}) => {
  const environments = useEnvironments();

  return (
    <>
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
    </>
  );
};

export default DefineEnvironmentFormSection;
```

### SelectClusterFormSection

```typescript jsx
import * as React from 'react';
import { ExpandableSection, SelectVariant, Spinner, Title } from '@patternfly/react-core';
import { useField } from 'formik';
import { SelectInputField } from '../../../shared';
import { EnvironmentFormData } from './types';
import useClusters from './useClusters';
import useContinuousStatus from './useContinuousStatus';
import { createClusterOptions } from './utils';

const SelectClusterFormSection: React.FC = () => {
  const [isExpanded, setExpanded] = React.useState(false);
  const clusters = useClusters();
  const [{ value }] = useField<EnvironmentFormData['cluster']>('cluster');
  const selectedCluster = clusters.find((cluster) => cluster.name === value);
  useContinuousStatus({ missingClusterList: clusters.length === 0 });

  return (
    <>
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
    </>
  );
};

export default SelectClusterFormSection;
```
