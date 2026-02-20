<script setup lang="ts">
import { h, resolveComponent } from 'vue'
import { upperFirst } from 'scule'
import type { TableColumn } from '@nuxt/ui'

const UButton = resolveComponent('UButton')
const UBadge = resolveComponent('UBadge')
const UDropdownMenu = resolveComponent('UDropdownMenu')

type REMITEvent = {
  asset: string
  eventid: string
  revision: number
  published: string
  start: string
  end: string
  installedcapacity: number
  availablecapacity: number
  unavailablecapacity: number
  type: string
  reason: string
  status:
    | 'Active'
    | 'Pending'
    | 'Suspended'
    | 'Inactive'
    | 'Cancelled'
    | 'Withdrawn'
}

const data = ref<REMITEvent[]>([
  {
    asset: 'COSO-1',
    eventid: '23XINTG-CROYTONG-ELXP-RMT-00034954',
    revision: 11,
    published: '2026-02-16 23:06',
    start: '2026-02-15 12:00',
    end: '2026-02-22 23:00',
    installedcapacity: 750,
    availablecapacity: 410,
    unavailablecapacity: 340,
    type: 'Special Information',
    reason: 'Forced Outage',
    status: 'Active'
  },
  {
    asset: 'ROCK-1',
    eventid: '48X000000000028Z-ELXP-RMT-00034769',
    revision: 1,
    published: '2026-02-18 14:28',
    start: '2026-03-06 13:00',
    end: '2026-03-06 14:00',
    installedcapacity: 748,
    availablecapacity: 573,
    unavailablecapacity: 175,
    type: 'Special Information',
    reason: 'Turbine',
    status: 'Pending'
  }, {
    asset: 'SPLN-1',
    eventid: '48X000000000038W-ELXP-RMT-00034496',
    revision: 5,
    published: '2025-06-10 11:01',
    start: '2025-12-25 12:00',
    end: '2025-12-26 17:30',
    installedcapacity: 800,
    availablecapacity: 0,
    unavailablecapacity: 800,
    type: 'Special Information',
    reason: 'Planned Outage',
    status: 'Cancelled'
  }

])

const columns: TableColumn<REMITEvent>[] = [
  {
    accessorKey: 'asset',
    header: 'Asset',
    cell: ({ row }) => `${row.getValue('asset')}`,
    enableSorting: true
  },
  {
    accessorKey: 'eventid',
    header: 'Event ID',
    cell: ({ row }) => `${row.getValue('eventid')}`
  },
  {
    accessorKey: 'revision',
    header: 'Event ID',
    cell: ({ row }) => `${row.getValue('revision')}`
  },
  {
    accessorKey: 'published',
    header: 'Published',
    cell: ({ row }) => {
      return new Date(row.getValue('published')).toLocaleString('en-GB', {
        day: 'numeric',
        month: 'numeric',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      })
    }
  },
  {
    accessorKey: 'start',
    header: 'Start',
    cell: ({ row }) => {
      return new Date(row.getValue('start')).toLocaleString('en-GB', {
        day: 'numeric',
        month: 'numeric',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      })
    }
  },
  {
    accessorKey: 'end',
    header: 'End',
    cell: ({ row }) => {
      return new Date(row.getValue('end')).toLocaleString('en-GB', {
        day: 'numeric',
        month: 'numeric',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
        hour12: false
      })
    }
  },
  {
    accessorKey: 'installedcapacity',
    header: 'Inst. (MW)',
    cell: ({ row }) => `${row.getValue('installedcapacity')}`
  },
  {
    accessorKey: 'availablecapacity',
    header: 'Avail. (MW)',
    cell: ({ row }) => `${row.getValue('availablecapacity')}`
  },
  {
    accessorKey: 'unavailablecapacity',
    header: 'Unavail. (MW)',
    cell: ({ row }) => `${row.getValue('unavailablecapacity')}`
  },
  {
    accessorKey: 'type',
    header: 'Type',
    cell: ({ row }) => `${row.getValue('type')}`
  },
  {
    accessorKey: 'reason',
    header: 'Reason',
    cell: ({ row }) => `${row.getValue('reason')}`
  },
  {
    accessorKey: 'status',
    header: 'Status',
    cell: ({ row }) => {
      const color = {
        Active: 'success' as const,
        Pending: 'neutral' as const,
        Suspended: 'neutral' as const,
        Inactive: 'error' as const,
        Cancelled: 'error' as const,
        Withdrawn: 'neutral' as const

      }[row.getValue('status') as string]

      return h(UBadge, { class: 'capitalize', variant: 'subtle', color }, () =>
        row.getValue('status')
      )
    }
  },
  {
    id: 'actions',
    enableHiding: false,
    meta: {
      class: {
        td: 'text-right'
      }
    },
    cell: ({ row }) => {
      const items = [
        {
          type: 'label',
          label: 'Actions'
        },
        {
          label: row.getIsExpanded() ? 'Collapse' : 'Expand',
          onSelect() {
            row.toggleExpanded()
          }
        }
      ]

      return h(
        UDropdownMenu,
        {
          'content': {
            align: 'end'
          },
          items,
          'aria-label': 'Actions dropdown'
        },
        () =>
          h(UButton, {
            'icon': 'i-lucide-ellipsis-vertical',
            'color': 'neutral',
            'variant': 'ghost',
            'aria-label': 'Actions dropdown'
          })
      )
    }
  }
]

const table = useTemplateRef('table')
</script>

<template>
  <div class="flex-1 divide-y divide-accented w-full">
    <div class="flex items-center gap-2 px-4 py-3.5 overflow-x-auto">
      <UFormField label="Search">
        <UInput
          :model-value="table?.tableApi?.getColumn('asset')?.getFilterValue() as string
          "
          class="max-w-sm min-w-[12ch]"
          variant="subtle"
          placeholder="Event ID, asset..."
          @update:model-value="
            table?.tableApi?.getColumn('asset')?.setFilterValue($event)
          "
        />
      </UFormField>
      <UFormField label="Asset">
        <UInputMenu
          :model-value="table?.tableApi?.getColumn('asset')?.getFilterValue() as string
          "
          class="max-w-sm min-w-[12ch]"
          variant="subtle"
          placeholder="Asset"
          default-value="All assets"
          @update:model-value="
            table?.tableApi?.getColumn('asset')?.setFilterValue($asset)
          "
        />
      </UFormField>
      <UFormField label="Status">
        <UInputMenu
          :model-value="table?.tableApi?.getColumn('status')?.getFilterValue() as string
          "
          class="max-w-sm min-w-[12ch]"
          variant="subtle"
          placeholder="Status"
          default-value="All statuses"
          @update:model-value="
            table?.tableApi?.getColumn('status')?.setFilterValue($asset)
          "
        />
      </UFormField>
      <UFormField label="Start from">
        <UInputDate
          :model-value="table?.tableApi?.getColumn('end')?.getFilterValue() as string"
          class="max-w-sm min-w-[12ch]"
          variant="subtle"
        >
          <UPopover :reference="inputDate?.inputsRef[3]?.$el">
            <UButton
              color="neutral"
              variant="link"
              size="sm"
              icon="i-lucide-calendar"
              aria-label="Select a date"
              class="px-0"
            />

            <template #content>
              <UCalendar
                v-model="modelValue"
                class="p-2"
              />
            </template>
          </UPopover>
        </UInputDate>
      </UFormField>
      <UFormField label="End before">
        <UInputDate
          :model-value="table?.tableApi?.getColumn('end')?.getFilterValue() as string"
          class="max-w-sm min-w-[12ch]"
          variant="subtle"
        >
          <UPopover :reference="inputDate?.inputsRef[3]?.$el">
            <UButton
              color="neutral"
              variant="link"
              size="sm"
              icon="i-lucide-calendar"
              aria-label="Select a date"
              class="px-0"
            />

            <template #content>
              <UCalendar
                v-model="modelValue"
                class="p-2"
              />
            </template>
          </UPopover>
        </UInputDate>
      </UFormField>

      <UDropdownMenu
        :items="table?.tableApi
          ?.getAllColumns()
          .filter((column) => column.getCanHide())
          .map((column) => ({
            label: upperFirst(column.id),
            type: 'checkbox' as const,
            checked: column.getIsVisible(),
            onUpdateChecked(checked: boolean) {
              table?.tableApi
                ?.getColumn(column.id)
                ?.toggleVisibility(!!checked);
            },
            onSelect(e: Event) {
              e.preventDefault();
            }
          }))
        "
        :content="{ align: 'end' }"
      >
        <UButton
          label="Columns"
          color="neutral"
          variant="outline"
          trailing-icon="i-lucide-chevron-down"
          class="ml-auto"
          aria-label="Columns select dropdown"
        />
      </UDropdownMenu>
    </div>

    <UTable
      ref="table"
      :data="data"
      :columns="columns"
      sticky
      class="h-96"
    >
      <template #expanded="{ row }">
        <pre>{{ row.original }}</pre>
      </template>
    </UTable>

    <div class="px-4 py-3.5 text-sm text-muted">
      Total entries: {{ table?.tableApi?.getFilteredRowModel().rows.length || 0 }}
    </div>
  </div>
</template>
