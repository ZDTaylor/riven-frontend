<script lang="ts">
	import * as AlertDialog from '$lib/components/ui/alert-dialog';
	import { Button } from '$lib/components/ui/button';
	import { toast } from 'svelte-sonner';
	import { getExternalID } from '$lib/tmdb';
	import { ItemsService } from '$lib/client';

	// eslint-disable-next-line @typescript-eslint/no-explicit-any
	export let data: any;
	export let type: string;

	async function requestItem(id: number) {
		const externalIds = await getExternalID(fetch, type, id);
		const response = await ItemsService.addItems({
			query: {
				imdb_ids: externalIds.imdb_id
			}
		});

		if (!response.error) {
			toast.success('Media requested successfully');
		} else {
			toast.error('An error occurred while requesting the media');
		}
	}
</script>

<AlertDialog.Root>
	<AlertDialog.Trigger asChild let:builder>
		<Button
			on:click={(e) => {
				e.preventDefault();
				e.stopPropagation();
			}}
			variant="outline"
			builders={[builder]}>Request</Button
		>
	</AlertDialog.Trigger>
	<AlertDialog.Content>
		<AlertDialog.Header>
			<AlertDialog.Title
				>Requesting {data.title || data.name || data.original_name}</AlertDialog.Title
			>
		</AlertDialog.Header>
		<AlertDialog.Footer>
			<AlertDialog.Cancel>Cancel</AlertDialog.Cancel>
			<AlertDialog.Action
				on:click={async () => {
					await requestItem(data.id);
				}}>Continue</AlertDialog.Action
			>
		</AlertDialog.Footer>
	</AlertDialog.Content>
</AlertDialog.Root>
