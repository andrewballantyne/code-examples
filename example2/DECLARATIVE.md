# Example 2

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
