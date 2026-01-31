<script lang="ts">
	import { page } from '$app/stores';
	import Icon from '@iconify/svelte';
	
	// Récupération du code d'erreur et du message
	$: status = $page.status;
	$: message = $page.error?.message || 'Une erreur est survenue';
	
	// Configuration des messages selon le type d'erreur
	const errorConfig: Record<number, { title: string; description: string; icon: string; color: string }> = {
		400: {
			title: 'Requête invalide',
			description: 'La requête envoyée est incorrecte ou mal formée.',
			icon: 'mdi:alert-circle-outline',
			color: 'warning'
		},
		401: {
			title: 'Non autorisé',
			description: 'Vous devez être connecté pour accéder à cette page.',
			icon: 'mdi:lock-outline',
			color: 'error'
		},
		403: {
			title: 'Accès refusé',
			description: "Vous n'avez pas la permission d'accéder à cette ressource.",
			icon: 'mdi:cancel',
			color: 'error'
		},
		404: {
			title: 'Page introuvable',
			description: "La page que vous recherchez n'existe pas ou a été déplacée.",
			icon: 'mdi:magnify',
			color: 'info'
		},
		500: {
			title: 'Erreur serveur',
			description: 'Une erreur interne est survenue. Veuillez réessayer plus tard.',
			icon: 'mdi:server-network-off',
			color: 'error'
		},
		502: {
			title: 'Mauvaise passerelle',
			description: 'Le serveur a reçu une réponse invalide.',
			icon: 'mdi:router-wireless-off',
			color: 'error'
		},
		503: {
			title: 'Service indisponible',
			description: 'Le service est temporairement indisponible. Veuillez réessayer plus tard.',
			icon: 'mdi:wrench-clock',
			color: 'warning'
		}
	};
	
	// Récupération de la configuration ou utilisation d'une valeur par défaut
	$: errorInfo = errorConfig[status] || {
		title: 'Erreur',
		description: message,
		icon: 'mdi:alert-octagon',
		color: 'error'
	};
</script>

<svelte:head>
	<title>{status} - {errorInfo.title}</title>
</svelte:head>

<div class="min-h-screen bg-base-200 flex items-center justify-center p-4">
	<div class="max-w-2xl w-full">
		<!-- Card principale -->
		<div class="card bg-base-100 shadow-2xl">
			<div class="card-body items-center text-center p-8 md:p-12">
				<!-- Icône animée -->
				<div class="text-8xl mb-6 animate-bounce">
					<Icon icon={errorInfo.icon} class="w-32 h-32 mx-auto text-{errorInfo.color}" />
				</div>
				
				<!-- Code d'erreur -->
				<div class="badge badge-{errorInfo.color} badge-lg text-xl font-bold px-6 py-4 mb-4">
					Erreur {status}
				</div>
				
				<!-- Titre -->
				<h1 class="text-4xl md:text-5xl font-bold mb-4">
					{errorInfo.title}
				</h1>
				
				<!-- Description -->
				<p class="text-lg text-base-content/70 mb-8 max-w-md">
					{errorInfo.description}
				</p>
				
				<!-- Message d'erreur détaillé (si disponible) -->
				{#if message && message !== errorInfo.description}
					<div class="alert alert-{errorInfo.color} mb-8 max-w-md">
						<Icon icon="mdi:alert" class="w-6 h-6" />
						<span class="text-sm">{message}</span>
					</div>
				{/if}
				
				<!-- Boutons d'action -->
				<div class="card-actions flex-col sm:flex-row gap-4 w-full sm:w-auto">
					<button 
						class="btn btn-primary btn-lg"
						on:click={() => window.history.back()}
					>
						<Icon icon="mdi:arrow-left" class="w-6 h-6" />
						Retour
					</button>
					
					<a href="/" class="btn btn-outline btn-lg">
						<Icon icon="mdi:home" class="w-6 h-6" />
						Accueil
					</a>
				</div>
				
				<!-- Suggestions supplémentaires pour 404 -->
				{#if status === 404}
					<div class="divider mt-8">Suggestions</div>
					<div class="text-sm text-base-content/60">
						<p>Vérifiez l'URL ou utilisez la navigation pour trouver ce que vous cherchez.</p>
					</div>
				{/if}
			</div>
		</div>
		
		<!-- Footer avec informations supplémentaires -->
		<div class="text-center mt-8 text-sm text-base-content/50">
			<p>Si le problème persiste, contactez le support technique.</p>
		</div>
	</div>
</div>

<style>
	/* Animation supplémentaire pour l'icône */
	@keyframes bounce {
		0%, 100% {
			transform: translateY(0);
		}
		50% {
			transform: translateY(-20px);
		}
	}
	
	.animate-bounce {
		animation: bounce 2s infinite;
	}
</style>