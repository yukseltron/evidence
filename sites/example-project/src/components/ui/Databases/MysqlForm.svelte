<script>
	export let credentials;
	export let existingCredentials;
    export let disableSave;

	credentials = { ...existingCredentials };

    credentials = {
        host: credentials.host,
        database: credentials.database,
        user: credentials.user,
        password: credentials.password,
        socketPath: credentials.socketPath
    }

    let opts = [
        {
            id: "host", 
            label: "Host", 
            type: "text", 
            optional: true, 
            override: false,
            placeholder: "database.server.com", 
            value: credentials.host ?? ""
        },
        {
            id: "database", 
            label: "Database", 
            optional: false, 
            override: false,
            placeholder: "my-database-name", 
            type: "text", 
            value: credentials.database ?? ""
        },
        {
            id: "user", 
            label: "User", 
            type: "text", 
            optional: false, 
            override: false,
            placeholder: "username", 
            value: credentials.user ?? ""
        },
        {
            id: "password", 
            label: "Password", 
            type: "password", 
            optional: false, 
            override: false,
            placeholder: "password", 
            value: credentials.password ?? ""
        },
        {
            id: "socketPath", 
            label: "Socket Path", 
            type: "text", 
            additionalInstructions:
				'This is an optional field. When using Google Cloud MySQL this is commonly required.',
            optional: true, 
            override: false,
            placeholder: "/cloudsql/my-project-123:us-northeast2:my-instance",
            value: credentials.socketPath ?? ""
        }
    ]
    import GenericForm from './GenericForm.svelte'

</script>

<GenericForm {opts} bind:credentials={credentials} bind:disableSave/>